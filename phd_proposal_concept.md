# PhD Concept Proposal

## Interpretable Machine Learning for Compositional Microbiome Data:
### Predicting BV Treatment Outcomes in South African Women

---

## 1. Background

### 1.1 Bacterial Vaginosis — A Persistent Clinical Challenge

Bacterial vaginosis (BV) is the most common cause of vaginal discharge among reproductive-age women globally. It is caused by an overgrowth of anaerobic bacteria in the vaginal microbiome, leading to a shift away from a healthy *Lactobacillus*-dominant state toward a dysbiotic state dominated by organisms such as *Gardnerella vaginalis*, *Atopobium vaginae*, and *Prevotella* spp.

Standard treatment with oral metronidazole (MTZ) or intravaginal clindamycin achieves initial cure rates of only 40–60%, with recurrence rates as high as 60% within 3–6 months (Bradshaw & Sobel, 2016). In South Africa, the burden is especially high — prevalence estimates range from 32% to over 50% in community and clinic-attending cohorts (NICD, 2025).

The CAPRISA 098 (CAP098) study, the source of data for this PhD, evaluated sequential therapy (oral MTZ followed by clindamycin for non-responders) in 100 women in Durban, South Africa. The results were striking: only 20% cleared BV after MTZ, and 70% remained BV-positive at Day 24 despite sequential treatment. This underscores the urgent need for better tools to predict who will respond to standard therapy and who will need alternative approaches.

### 1.2 Compositional Nature of Microbiome Data

A fundamental property of microbiome data is that it is **compositional** — ASV/OTU abundances are relative proportions, not absolute counts. If one taxon increases, others must decrease, regardless of biological reality. This creates a **unit-sum constraint** (also called the "closure problem"): each sample's abundances sum to 1 (or 100%).

Standard statistical methods (means, correlations, covariances) applied to raw relative abundances produce spurious results (Aitchison, 1982). Common workarounds include the centered log-ratio (CLR) transformation, which maps compositional data to real space, but even then, the underlying **dependence structure** between taxa persists.

### 1.3 Machine Learning for Clinical Prediction

Machine learning methods — random forests, gradient boosting (XGBoost, LightGBM), support vector machines, and neural networks — have been widely applied to clinical prediction problems. They offer superior predictive performance compared to traditional logistic regression, especially when the relationship between features and outcomes is complex and non-linear.

In microbiome research, ML models have been used to predict disease states, treatment responses, and clinical outcomes from taxonomic profiles (Statnikov et al., 2013; Pasolli et al., 2016). However, the focus has largely been on prediction accuracy, with less attention to *how* these models arrive at their predictions.

### 1.4 The Interpretability Problem

As ML models grow more complex, they become harder to explain. This is a critical barrier in clinical settings where doctors, regulators, and patients need to trust the model's recommendations.

Two dominant approaches have emerged for post-hoc interpretability:

- **SHAP (SHapley Additive exPlanations)** — Based on cooperative game theory, SHAP attributes each feature's contribution to a prediction by averaging over all possible feature subsets (Lundberg & Lee, 2017).
- **LIME (Local Interpretable Model-agnostic Explanations)** — Approximates the complex model with a simpler, interpretable model (e.g., linear regression) in the neighborhood of a specific prediction (Ribeiro et al., 2016).

**The critical gap:** Both SHAP and LIME assume features are independent. In compositional data, features are fundamentally interdependent — changing one taxon's abundance forces changes in others. Applying SHAP or LIME to microbiome data yields explanations that are **compositionally invalid** and potentially misleading.

### 1.5 Why This Gap Matters

If a model predicts "this woman will not respond to MTZ," a clinician wants to know *why*. Current tools might say:

> *"Gardnerella abundance is the most important factor"*

But this ignores that *Gardnerella*'s importance depends on what else is in the sample. A high *Gardnerella* count means different things when accompanied by *Lactobacillus crispatus* vs. *Atopobium vaginae*. The relative ratios matter, and no existing interpretability method accounts for this.

---

## 2. Rationale

Despite growing interest in both microbiome-based prediction and ML interpretability, no existing method produces explanations that respect the compositional structure of microbiome data. This is a concrete, solvable problem with wide applicability.

**Why this dataset is ideal:**
- Real clinical data with documented treatment failure
- Multiple time points (Day 0, 8, 16, 24) enabling longitudinal comparison
- Both microbiome (ASV/OTU) and clinical features available
- Multi-class outcome (clearance, persistence, recurrence) reflecting real clinical complexity
- A clear "before vs. after" treatment comparison point (Day 0 vs. Day 8)

**Why the method is novel:**
- Addresses a fundamental assumption (feature independence) that is violated by compositional data
- Could be extended beyond microbiome to any compositional data (geochemistry, economics, text analysis)
- Bridges two active research communities: compositional data analysis and explainable AI

---

## 3. Research Objectives

### Primary Objective
To develop a novel interpretability framework for machine learning models applied to compositional data, producing explanations that are both accurate and compositionally valid.

### Secondary Objective
To apply this framework to predict BV treatment outcomes (clearance, persistence, recurrence) in the CAP098 cohort, comparing prediction at baseline (Day 0) versus after first-line treatment (Day 8).

---

## 4. Specific Aims

**Aim 1: Develop a compositional-aware interpretability method**
Design and implement a post-hoc interpretability framework that respects the unit-sum constraint and dependence structure of compositional data. This may involve:
- Defining feature importance in terms of log-ratios rather than absolute abundances
- Developing a compositional analogue of Shapley values
- Creating a perturbation scheme for LIME that preserves compositional structure

**Aim 2: Validate the method through simulation studies**
Simulate compositional datasets with known ground-truth feature importance. Compare the proposed method against standard SHAP and LIME to demonstrate:
- Recovery of true important features
- Compositional validity of explanations
- Robustness to varying numbers of taxa and sample sizes

**Aim 3: Predict BV outcomes at baseline (Day 0)**
Using baseline microbiome (ASV/OTU) and clinical data, train ML models to predict 3-way outcomes (clearance, persistence, recurrence) at Day 24. Apply and evaluate the new interpretability method.

**Aim 4: Predict BV outcomes after first-line treatment (Day 8)**
Using Day 0 + Day 8 data, repeat the prediction task and compare:
- Does prediction accuracy improve with early treatment response data?
- Do the important features change between Day 0 and Day 8?
- Does the interpretability method reveal different biological insights at each time point?

**Aim 5: Demonstrate generalizability**
Apply the method to a second independent microbiome dataset (e.g., gut microbiome and colorectal cancer, or soil microbiome and environmental response) to show the framework is not disease- or site-specific.

---

## 5. Literature Review Structure

The literature review will be organized into six sections:

### Chapter 2.1: Bacterial Vaginosis and Treatment Failure
- Epidemiology of BV globally and in South Africa
- Standard treatment regimens and their limitations
- The CAP098 study in context
- Biological mechanisms of treatment failure (biofilms, resistance, microbiome instability)

### Chapter 2.2: The Vaginal Microbiome
- Community State Types (CSTs) and their clinical significance
- *Lactobacillus* species: *L. crispatus* vs. *L. iners* and stability
- Role of *Gardnerella*, *Atopobium*, *Prevotella*, and other anaerobes
- Longitudinal dynamics of the vaginal microbiome

### Chapter 2.3: Compositional Data Analysis
- Aitchison's framework: the simplex, log-ratio transformations
- The closure problem and why standard statistics fail
- CLR, ALR, and ILR transformations
- Compositional data in high-dimensional settings (p >> n)

### Chapter 2.4: Machine Learning for Clinical Prediction
- Overview of supervised learning methods
- Tree-based methods (random forest, XGBoost, LightGBM)
- Handling class imbalance (recurrence is rare — 7–10%)
- Evaluation metrics for multi-class prediction
- Feature selection in high-dimensional settings

### Chapter 2.5: Interpretable Machine Learning
- Intrinsic interpretability vs. post-hoc explanations
- Global vs. local explanations
- SHAP (Shapley values) — theory and limitations
- LIME — theory and limitations
- Other approaches: PDP, ICE, permutation importance, surrogate models

### Chapter 2.6: The Gap — Compositionality and Interpretability
- Why SHAP/LIME assume feature independence
- Consequences for microbiome data: documented examples of misleading explanations
- Existing attempts to address this (if any)
- The opportunity for a novel contribution

---

## 6. Methods

### 6.1 Data

**CAP098 Dataset**
- 100 women, 4 visits (Day 0, 8, 16, 24)
- Microbiome data: ASV/OTU abundance tables (compositional)
- Clinical data: age, HIV status, education, income, Nugent score, Amsel criteria, weight, symptoms
- Outcome variable: 3-class outcome at Day 24
  - **Clearance** (Nugent 0–3 or Amsel < 3)
  - **Persistence** (Nugent 4–10 throughout)
  - **Recurrence** (cleared then returned)

### 6.2 Preprocessing

- ASV/OTU counts: rarefaction or CSS normalization
- CLR transformation for compositional data
- Filtering: remove taxa present in < 10% of samples
- Clinical features: encode categorical variables, scale continuous variables
- Handle missing data (3 lost to follow-up — exclude from outcome analysis)

### 6.3 Machine Learning Models

Three model classes will be compared:

| Model | Strengths | Weaknesses |
|-------|-----------|------------|
| **Logistic Regression (multinomial)** | Highly interpretable, compositional covariates can be log-ratios | Limited to linear relationships |
| **Random Forest** | Handles non-linearity, feature interactions, high dimensions | Less interpretable, can overfit |
| **XGBoost** | State-of-the-art performance, handles missing data | Black-box, needs tuning |

Training will use 5-fold cross-validation with class imbalance handled via:
- SMOTE (Synthetic Minority Oversampling)
- Class weights
- Stratified sampling

### 6.4 New Interpretability Method — Outline

The core idea is to replace **feature-level importance** (e.g., "*Gardnerella* importance = 0.3") with **log-ratio-level importance** (e.g., "*Lactobacillus* / *Gardnerella* ratio importance = 0.3").

**Approach A: Compositional Shapley Values**
- Instead of computing Shapley values for individual taxa, compute them for **log-ratio contrasts** between taxa or groups of taxa
- This respects that information in compositional data lies in relative proportions, not absolute abundances

**Approach B: Ratio-Based LIME**
- Instead of perturbing individual features independently (which breaks compositionality), perturb by **multiplying by random compositional factors** (simulating draws from a Dirichlet distribution centered on the original composition)
- The local surrogate model is trained on log-ratio features rather than absolute features

**Approach C: Compositional Permutation Importance**
- Permutation importance randomly shuffles feature values, but this destroys compositional structure. Instead, **permute within the simplex** — shuffle log-ratios between samples while preserving total composition

Each approach will be implemented, compared, and the most promising one will become the final method.

### 6.5 Evaluation Strategy

**Simulation evaluation (Aim 2):**
- Generate synthetic compositional data with known important features
- Compare proposed method vs. SHAP vs. LIME
- Metrics: rank correlation with true importance, stability across perturbations, compositional validity

**Prediction evaluation (Aim 3 & 4):**
- Primary: multi-class AUC (one-vs-one or one-vs-rest)
- Secondary: accuracy, precision, recall, F1 per class
- Compare: Day 0 prediction vs. Day 8 prediction

**Interpretability evaluation:**
- Stability: do explanations change drastically with small data perturbations?
- Biological plausibility: do the important features align with known BV biology?
- Clinical usefulness: could a clinician act on these explanations?

### 6.6 Software Implementation

- R (tidyverse, tidymodels, caret, xgboost, randomForest)
- Compositional data: compositions, robCompositions, zCompositions
- Interpretability: SHAPforxgboost, lime, DALEX
- The new method will be implemented as an R package (to be submitted to CRAN)

---

## 7. Thesis Structure

| Chapter | Title | Content |
|---------|-------|---------|
| **1** | Introduction | Problem statement, research questions, thesis outline |
| **2** | Literature Review | BV epidemiology, vaginal microbiome, compositional data, ML interpretability, the gap |
| **3** | A Compositional-Aware Interpretability Framework | The new method — theory, algorithms, implementation |
| **4** | Simulation Validation | Synthetic data experiments comparing against SHAP/LIME |
| **5** | Predicting BV Outcomes: Baseline (Day 0) | ML models + interpretability at baseline |
| **6** | Predicting BV Outcomes: After First Treatment (Day 8) | ML models + interpretability, comparison with Day 0 |
| **7** | Generalizability to Other Microbiome Datasets | Application to a second dataset |
| **8** | Discussion | Summary of contributions, clinical implications, limitations, future work |

### Timeline (Suggested)

| Year | Work |
|------|------|
| **Year 1** | Literature review, data familiarization, implement compositional interpretability method, simulation study |
| **Year 2** | Apply method to CAP098 (Day 0 and Day 8), write Chapters 3–6 |
| **Year 3** | Generalizability study, write Chapters 7–8, thesis writing and defense |

---

## 8. Expected Contributions

### Theoretical
- A principled framework for interpreting ML models on compositional data
- Bridging compositional data analysis and explainable AI

### Methodological
- A new algorithm (or family of algorithms) for compositional-aware interpretability
- An R package implementing the method

### Applied
- Clinically useful insights into predictors of BV treatment outcomes
- A comparison of predictive power at baseline vs. after first-line treatment
- Identification of women who may need alternative therapy

---

## 9. Open Questions to Explore

1. **Log-ratio selection:** With hundreds of taxa, there are thousands of possible log-ratios. How do we efficiently search for informative ones?
2. **Clinical vs. microbiome features:** Should they be combined in one model, or should microbiome and clinical explanations be handled differently?
3. **Threshold effects:** Could there be a "tipping point" where a ratio crosses a threshold (e.g., *Lactobacillus* falls below 10% of total), and does this matter for interpretability?
4. **Model dependence:** Does the best interpretability approach depend on the underlying ML model (tree vs. linear vs. neural network)?
5. **Validation with domain experts:** How do we formally evaluate whether a clinician finds the explanations useful?

---

## 10. Preliminary References

Aitchison, J. (1982). The statistical analysis of compositional data. *Journal of the Royal Statistical Society: Series B*, 44(2), 139–160.

Bradshaw, C. S., & Sobel, J. D. (2016). Current treatment of bacterial vaginosis — limitations and need for innovation. *The Journal of Infectious Diseases*, 214(suppl_1), S14–S20.

Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. *Advances in Neural Information Processing Systems*, 30.

Ribeiro, M. T., Singh, S., & Guestrin, C. (2016). "Why should I trust you?" Explaining the predictions of any classifier. *Proceedings of the 22nd ACM SIGKDD*, 1135–1144.

Statnikov, A., et al. (2013). A comprehensive evaluation of multicategory classification methods for microbiomic data. *Microbiome*, 1(1), 1–12.

Pasolli, E., et al. (2016). Machine learning meta-analysis of large metagenomic datasets: tools and biological insights. *PLoS Computational Biology*, 12(7), e1004977.

Gloor, G. B., et al. (2017). Microbiome datasets are compositional: and this is not optional. *Frontiers in Microbiology*, 8, 2224.

Quinn, T. P., et al. (2018). A field guide for the compositional analysis of any-omics data. *GigaScience*, 8(2), giz107.

*(Full reference list to be expanded during Year 1 literature review)*