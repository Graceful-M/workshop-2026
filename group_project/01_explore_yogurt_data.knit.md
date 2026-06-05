---
title: "Group 1 — Yogurt Study Data Exploration"
author: "Workshop Participant"
format:
  html:
    toc: true
    code-fold: true
    embed-resources: true
---

## Overview

This document explores the **Yogurt study dataset** (Group A / Group 1).
The study investigates how eating yogurt affects the vaginal microbiome.
Participants were split into two **arms**: a **yogurt** group and an
**unchanged diet** group. Samples were taken at two **time points**:
baseline and after antibiotics.

The data is split across four CSV files, linked by participant IDs
(`pid`) and sample IDs (`sample_id`).

---

## Load libraries


::: {.cell}

```{.r .cell-code}
library(tidyverse)
library(knitr)
```
:::


---

## File 1 — Sample IDs (`00_sample_ids_yogurt.csv`)

Each row is **one sample** from one participant at one time point.

| Column | Meaning |
|--------|---------|
| `pid` | Participant ID (e.g. `pid_25`). Links to the metadata file. |
| `time_point` | When the sample was taken — `baseline` (before study) or `after_antibiotic` (after a course of antibiotics). |
| `arm` | Which group the participant was assigned to — `yogurt` or `unchanged_diet`. |
| `sample_id` | Unique lab sample identifier (e.g. `YOG002`). Links to qPCR and Luminex results. |


::: {.cell}

```{.r .cell-code}
sample_ids <- read_csv("../data/group_a_yogurt/00_sample_ids_yogurt.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 102 Columns: 4
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (4): pid, time_point, arm, sample_id

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
glimpse(sample_ids)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 102
Columns: 4
$ pid        <chr> "pid_25", "pid_51", "pid_33", "pid_13", "pid_37", "pid_09",…
$ time_point <chr> "baseline", "baseline", "baseline", "baseline", "after_anti…
$ arm        <chr> "unchanged_diet", "yogurt", "yogurt", "yogurt", "unchanged_…
$ sample_id  <chr> "UNCH001", "YOG002", "YOG003", "YOG004", "UNCH005", "UNCH00…
```


:::
:::



::: {.cell}

```{.r .cell-code}
# Count samples by time point and arm
sample_ids |> 
  count(time_point, arm)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 4 × 3
  time_point       arm                n
  <chr>            <chr>          <int>
1 after_antibiotic unchanged_diet    25
2 after_antibiotic yogurt            26
3 baseline         unchanged_diet    25
4 baseline         yogurt            26
```


:::
:::


---

## File 2 — Participant Metadata (`01_participant_metadata_yogurt.csv`)

Each row is **one participant** with their demographic and health info.

| Column | Meaning |
|--------|---------|
| `pid` | Participant ID. Links to the sample IDs file. |
| `arm` | Which group — `yogurt` or `unchanged_diet`. |
| `days_since_last_sex` | Days since the participant last had sex (integer). |
| `birth_control` | Type of birth control — `no hormonal birth control` or `Depoprovera` (a hormonal injectable). |
| `age` | Age in years. |
| `education` | Education level (self-reported, categorical). |
| `sex` | Sex of the participant (all recorded as `female`). |


::: {.cell}

```{.r .cell-code}
metadata <- read_csv("../data/group_a_yogurt/01_participant_metadata_yogurt.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 51 Columns: 7
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (5): pid, arm, birth_control, education, sex
dbl (2): days_since_last_sex, age

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
glimpse(metadata)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 51
Columns: 7
$ pid                 <chr> "pid_01", "pid_02", "pid_03", "pid_04", "pid_05", …
$ arm                 <chr> "unchanged_diet", "unchanged_diet", "unchanged_die…
$ days_since_last_sex <dbl> 2, 4, 3, 1, 5, 9, 3, 4, 10, 7, 9, 20, 1, 1, 11, 3,…
$ birth_control       <chr> "no hormonal birth control", "Depoprovera", "no ho…
$ age                 <dbl> 28, 34, 32, 30, 34, 27, 25, 26, 34, 32, 32, 32, 30…
$ education           <chr> "grade 10-12, not matriculated", "less than grade …
$ sex                 <chr> "female", "female", "female", "female", "female", …
```


:::
:::



::: {.cell}

```{.r .cell-code}
# Age distribution by arm
ggplot(metadata, aes(x = arm, y = age, fill = arm)) +
  geom_boxplot() +
  labs(title = "Age distribution by study arm", y = "Age (years)", x = NULL) +
  theme_minimal()
```

::: {.cell-output-display}
![](01_explore_yogurt_data_files/figure-html/unnamed-chunk-5-1.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
# Birth control by arm
metadata |> 
  count(arm, birth_control) |> 
  kable()
```

::: {.cell-output-display}


|arm            |birth_control             |  n|
|:--------------|:-------------------------|--:|
|unchanged_diet |Depoprovera               |  8|
|unchanged_diet |no hormonal birth control | 17|
|yogurt         |Depoprovera               | 17|
|yogurt         |no hormonal birth control |  9|


:::
:::


---

## File 3 — qPCR Results (`02_qpcr_results_yogurt.csv`)

Each row is **one sample** with qPCR measurements of bacterial DNA.
qPCR (quantitative PCR) is a lab technique that measures how much of a
specific bacterium's DNA is present in a sample.

| Column | Meaning |
|--------|---------|
| `sample_id` | Lab sample ID. Links to the sample IDs file. |
| `qpcr_bacteria` | Amount of **total bacterial DNA** detected. |
| `qpcr_crispatus` | Amount of *Lactobacillus crispatus* DNA (a "good" bacteria linked to vaginal health). |
| `qpcr_iners` | Amount of *Lactobacillus iners* DNA (another *Lactobacillus* species, considered less protective). |


::: {.cell}

```{.r .cell-code}
qpcr <- read_csv("../data/group_a_yogurt/02_qpcr_results_yogurt.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 102 Columns: 4
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (1): sample_id
dbl (3): qpcr_bacteria, qpcr_crispatus, qpcr_iners

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
glimpse(qpcr)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 102
Columns: 4
$ sample_id      <chr> "UNCH092", "UNCH042", "UNCH015", "UNCH035", "UNCH019", …
$ qpcr_bacteria  <dbl> 3093064.20, 8275.04, 7532202.40, 4794320.40, 5328343.60…
$ qpcr_crispatus <dbl> 0.000, 0.000, 0.000, 12.994, 0.000, 0.000, 0.000, 38.58…
$ qpcr_iners     <dbl> 26.840, 76.040, 554.964, 204308.968, 107806.148, 121045…
```


:::
:::



::: {.cell}

```{.r .cell-code}
# Join qPCR results with sample info so we can compare arms
qpcr_with_arm <- sample_ids |> 
  left_join(qpcr, by = "sample_id")

# Compare total bacteria by arm and time point
ggplot(qpcr_with_arm, aes(x = arm, y = qpcr_bacteria, fill = arm)) +
  geom_boxplot() +
  facet_wrap(vars(time_point)) +
  scale_y_log10() +
  labs(title = "Total bacterial load by arm and time point",
       y = "Total bacteria (log scale)", x = NULL) +
  theme_minimal()
```

::: {.cell-output-display}
![](01_explore_yogurt_data_files/figure-html/unnamed-chunk-8-1.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
# Compare L. crispatus vs L. iners by arm
# Higher crispatus / lower iners is generally considered healthier
qpcr_with_arm |> 
  pivot_longer(cols = c(qpcr_crispatus, qpcr_iners),
               names_to = "bacteria",
               values_to = "count") |> 
  ggplot(aes(x = arm, y = count + 1, fill = bacteria)) +
  geom_boxplot() +
  facet_wrap(vars(time_point)) +
  scale_y_log10() +
  labs(title = "L. crispatus vs L. iners by arm and time point",
       y = "Bacterial DNA amount (+1, log scale)", x = NULL) +
  theme_minimal()
```

::: {.cell-output-display}
![](01_explore_yogurt_data_files/figure-html/unnamed-chunk-9-1.png){width=672}
:::
:::


---

## File 4 — Luminex Results (`03_luminex_results_yogurt.csv`)

Each row is **one cytokine measurement** for one sample. This is "long"
format — each sample appears in multiple rows, one per cytokine measured.
Luminex is a lab technique that measures multiple proteins (cytokines) at
once. Cytokines are signalling molecules that the immune system uses to
communicate — higher levels can mean more inflammation.

| Column | Meaning |
|--------|---------|
| `sample_id` | Lab sample ID. Links to the sample IDs file. |
| `cytokine` | Which cytokine was measured (e.g. `IL-1a`, `TNFa`, `IL-8`). 10 different cytokines in total. |
| `conc` | Concentration of that cytokine in the sample (units as provided by the lab). |
| `limits` | Whether the measurement was `within limits` (reliable) or `out of range` (below the test's detection limit). |


::: {.cell}

```{.r .cell-code}
luminex <- read_csv("../data/group_a_yogurt/03_luminex_results_yogurt.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 1020 Columns: 4
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (3): sample_id, cytokine, limits
dbl (1): conc

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
glimpse(luminex)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 1,020
Columns: 4
$ sample_id <chr> "UNCH092", "UNCH092", "UNCH092", "UNCH092", "UNCH092", "UNCH…
$ cytokine  <chr> "IL-1a", "IL-10", "IL-1b", "IL-8", "IL-6", "TNFa", "IP-10", …
$ conc      <dbl> 5.8500, 0.7670, 0.2505, 7.9200, 0.0935, 0.4710, 177.3800, 36…
$ limits    <chr> "within limits", "out of range", "out of range", "within lim…
```


:::
:::



::: {.cell}

```{.r .cell-code}
# List all cytokines measured
unique(luminex$cytokine)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "IL-1a"  "IL-10"  "IL-1b"  "IL-8"   "IL-6"   "TNFa"   "IP-10"  "MIG"   
 [9] "IFN-Y"  "MIP-3a"
```


:::
:::



::: {.cell}

```{.r .cell-code}
# How many measurements are within vs out of range?
luminex |> 
  count(limits)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 2
  limits            n
  <chr>         <int>
1 out of range    498
2 within limits   522
```


:::
:::



::: {.cell}

```{.r .cell-code}
# Join Luminex results with sample info
luminex_with_arm <- sample_ids |> 
  left_join(luminex, by = "sample_id")

# Look at a few key inflammatory cytokines across arms
key_cytokines <- c("IL-1b", "IL-6", "IL-8", "TNFa")

luminex_with_arm |> 
  filter(cytokine %in% key_cytokines, limits == "within limits") |> 
  ggplot(aes(x = arm, y = conc, fill = arm)) +
  geom_boxplot() +
  facet_grid(cytokine ~ time_point, scales = "free_y") +
  labs(title = "Key inflammatory cytokines by arm and time point",
       y = "Concentration", x = NULL) +
  theme_minimal()
```

::: {.cell-output-display}
![](01_explore_yogurt_data_files/figure-html/unnamed-chunk-13-1.png){width=672}
:::
:::


