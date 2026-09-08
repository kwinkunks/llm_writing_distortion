# LLM Writing Distortion
 
This repository is a modified fork of that accompanying the paper,  
**How LLMs Distort Our Written Language** (2026) by Marwa Abdulhai, Isadora White, Yanming Wan, Ibrahim Qureshi, Joel Z. Leibo, Max Kleiman-Weiner, Natasha Jaques. [Link to abstract.](https://arxiv.org/abs/2603.18161)

---
 
## Overview
 
We study these effects across three evaluation settings:

 - **Human Evaluation** — A randomized controlled trial where participants wrote an argumentative essay with or without access to an LLM.
- **ArgRewrite Analysis** — With a dataset of 86 human-written argumentative essays with expert feedback collected in 2021 (before the release of ChatGPT), we performed a counterfactual analysis comparing human revisions to revisions produced by three frontier LLMs (gpt-5-mini, gemini-2.5-flash, claude-3.5-haiku) across five revision types (general, minimal, grammar, completion, expansion).
- **ICLR Analysis** — An analysis of the strengths and weaknesses of 18k peer reviews from ICLR 2026 (where 21% were found to be LLM-generated).
 
---
 
## Repository Structure
 
```
llm_writing_distortion/
├── data/                       # All datasets and derived/generated artifacts
│   ├── ArgRewrite/             # ArgRewrite-v2 dataset (essays, feedback, annotations)
│   ├── NRC-Emotion-Lexicon/    # NRC Emotion Lexicon for sentiment/emotion scoring
│   ├── llm_drafts/<model>/     # LLM-generated revised drafts (reused, per model/mode)
│   ├── derived/                # Aggregated tables (e.g. dataframe_essays_llm_model.csv), LIWC-22.csv
│   └── iclr/                   # ICLR analysis outputs (strengths/weaknesses CSVs, figures)
├── notebooks/
│   ├── argrewrite/             # ArgRewrite analysis notebooks (semantic, emotions, JSD, POS, ...)
│   └── iclr/                   # ICLR peer-review analysis notebook
├── scripts/                    # (placeholder for reusable Python scripts)
├── pyproject.toml              # uv project (Python 3.13)
├── uv.lock
└── README.md
```

> **Note (this fork):** the repo was reorganized into `data/` + `notebooks/`, and the
> model backend was migrated from Google Gemini to a **Microsoft Azure AI Foundry**
> endpoint. So far the **ArgRewrite semantic-shift** analysis
> (`notebooks/argrewrite/LLM Homogenization - semantic.ipynb`) has been ported and
> reproduced; the other notebooks still use the original paths/backends and need the
> same treatment.
 
---
 
## Quick Start
 
### Installation

This fork uses [uv](https://docs.astral.sh/uv/) (Python 3.13):

```bash
uv sync
```

### Model backend (Azure AI Foundry)

Embeddings (and, later, draft generation) run against an Azure AI Foundry endpoint.
Create a `.env` file at the repo root (gitignored) with:

```bash
AZURE_OPENAI_ENDPOINT=https://<resource>.openai.azure.com/openai/v1
AZURE_OPENAI_API_KEY=<your-key>
AZURE_EMBEDDING_DEPLOYMENT=text-embedding-3-large   # or text-embedding-3-small
```

The endpoint uses the new `/openai/v1` API surface, so the notebooks talk to it with
the standard `openai` `OpenAI` client (`base_url=<endpoint>`), not `AzureOpenAI`.

### Running the Analysis

```bash
uv run jupyter lab      # then open a notebook under notebooks/
```

- **`notebooks/argrewrite/LLM Homogenization - semantic.ipynb`** — reproduces the
  semantic-shift result: embeds ArgRewrite drafts, fits a shared 2-component PCA per
  `(model, feedback)` panel, and reports **Avg Shift** (mean Euclidean D1→D2 / D1→AI
  displacement). Set `emb_label = "Azure"` for Foundry embeddings or `"MiniLM-L6"` for
  the offline local `all-MiniLM-L6-v2` backbone.
- **`notebooks/argrewrite/`** — sibling notebooks for emotion (NRC), lexical (JSD), and
  POS shifts *(not yet ported to the new layout/backend)*.
- **`notebooks/iclr/`** — ICLR peer-review strengths/weaknesses analysis *(not yet ported)*.

 
---
 
## Datasets
 
### ArgRewrite-v2
The [ArgRewrite-v2 corpus](http://argrewrite.cs.pitt.edu/) (Chen et al., 2022) contains 86 argumentative essays written by university students in 2021, each paired with expert feedback and a human-revised second draft. Because this dataset predates the release of ChatGPT, it enables a clean counterfactual comparison: what would a human have written versus what an LLM produces given the same essay and the same expert feedback.
 
### NRC Emotion Lexicon
The [NRC Word-Emotion Association Lexicon](https://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm) (Mohammad and Turney, 2013) maps English words to eight basic emotions (anger, anticipation, disgust, fear, joy, sadness, surprise, trust) and two sentiments (positive, negative). Used to quantify shifts in affective tone induced by LLM revisions relative to human edits.
 
### LIWC-22
The [Linguistic Inquiry and Word Count (LIWC-22)](https://www.liwc.app) tool (Boyd et al., 2022; Tausczik and Pennebaker, 2010) categorizes words across 90+ dimensions including summary variables (analytic thinking, clout, authenticity), grammatical categories (pronouns, prepositions), and psychological processes (cognitive mechanisms, social processes). Used to measure shifts in analytical thinking style and authenticity between human-written and LLM-edited text.
 
### ICLR 2026 Peer Reviews
Peer reviews from ICLR 2026, with LLM-generation labels from the [Pangram AI classifier](https://www.pangram.com/blog/pangram-predicts-21-of-iclr-reviews-are-ai-generated). We analyze 18k reviews drawn from papers that received exactly one fully human-written and one fully LLM-generated review, ensuring unbiased sampling across conditions.

---
 
## Citation
 
If you use this code or build on this analysis, please cite the associated work. 
