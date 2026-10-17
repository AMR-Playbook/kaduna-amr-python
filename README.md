<div align="center">

# 🐍 Kaduna AMR Watch — Analysis

**Every number rebuildable with one command. Every trend with its uncertainty. Every model with its limits.**

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![Data](https://img.shields.io/badge/data-SYNTHETIC%20practice-d29922?style=flat-square)
![Research only](https://img.shields.io/badge/models-research%20only-cf222e?style=flat-square)

</div>

> [!IMPORTANT]
> **SYNTHETIC PRACTICE DATA. RESEARCH ONLY.** Nothing in this repository is a clinical finding or a prescribing tool.

## Why this exists

If a funder asks *"how did you get 61.7%?"*, we must be able to show every step and rerun it in front of them. This repository:

1. **Reproduces** the antibiogram independently of Excel and SQL
2. **Separates real change from noise**, using confidence intervals and trend tests
3. **Estimates resistance risk** before culture results arrive, and tests honestly whether that estimate is safe

## Pipeline

```mermaid
flowchart LR
    SRC["🗄️ SQL views<br/>(read-only role)<br/>or Clean Handoff Pack"] --> LOAD["src/load.py"]
    LOAD --> CHK["tests/<br/>answer-key checks"]
    CHK --> AB["Antibiogram"]
    CHK --> TR["Trends<br/>Wilson 95% CI<br/>logistic trend test"]
    CHK --> ML["Risk model<br/>time split · calibration"]
    AB --> OUT["reports/"]
    TR --> OUT
    ML --> CARD["Model card"]
    OUT --> BI["📊 Power BI"]
```

## Model safety

```mermaid
flowchart TB
    subgraph ALLOWED["✅ Known when prescribing"]
        A1[ward] --- A2[specimen] --- A3[age] --- A4[sex] --- A5[prior antibiotic use] --- A6[hospital] --- A7[month]
    end
    subgraph LEAK["⛔ Never a feature: known only afterwards"]
        L1[other antibiotic results] --- L2[esbl_screen] --- L3[mdr_flag] --- L4[outcome] --- L5[length of stay]
    end
    ALLOWED --> M["Model trained 2020–2024<br/>tested on 2025–2026"]
```

## The answer key

`tests/` must check every row of sheet `11_ANSWER_KEY` in the [Clean Handoff Pack](https://github.com/AMR-Playbook/.github/releases/tag/excel-handoff-v2). The headline check is *E. coli* + ceftriaxone in 2024: **201 resistant of 326 tested (61.66%)**.

## Layout

```
data/raw/      downloaded source data (git-ignored, never edited)
data/clean/    derived tables (git-ignored)
src/           pipeline code: load, antibiogram, trends, model, run_all
tests/         answer-key and integrity tests
notebooks/     exploration only, numbered and runnable top to bottom
reports/       generated tables, figures, methods note, model card
```

## Getting started

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Then pick an [open issue](../../issues) and read [CONTRIBUTING](https://github.com/AMR-Playbook/.github/blob/main/CONTRIBUTING.md).
