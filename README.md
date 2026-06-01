# Data Science for Biology — Workshop Environment (June 2026)

This is the participant environment for the **Intro to R for Data Science
with LLMs** workshop. It runs entirely in **GitHub Codespaces** — there is
nothing to install on your computer. You only need a web browser and a free
GitHub account.

## Getting started

1. Click the green **"Use this template"** button (top right) →
   **"Create a new repository"** to make your own copy.
2. On *your copy*, add your OpenRouter API key as a Codespaces secret named
   `OPENROUTER_API_KEY` (your GitHub **Settings → Codespaces → Secrets**, and
   give the secret access to your new repository).
3. On your copy, click **"Code" → "Codespaces" → "Create codespace on main"**.
4. When the codespace finishes building, open `notebooks/00-hello-r.qmd` and
   run the code cell.
5. Open the terminal and type `claude` to start Claude Code, your AI coding
   assistant.

Full step-by-step walkthrough:
[Get Started page on the workshop website](https://elsherbini.github.io/data-science-for-biology/getting-started.html).

## What's inside

- `.devcontainer/` — defines the Codespace: R, the tidyverse, Quarto, and the
  Claude Code CLI.
- `notebooks/` — starter Quarto (`.qmd`) notebooks for the workshop.
- `data/` — put any data files you work with here.
- `CLAUDE.md` — context that Claude Code reads automatically so it can help
  you better.

## About the AI assistant

Claude Code is billed through your own OpenRouter account. Inside a Claude
Code session you can switch models with the `/model` command — try the
Haiku, Sonnet, and Opus tiers and notice the difference in speed, quality,
and cost.
