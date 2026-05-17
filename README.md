# FALA20x1: Fallacy And Topic Drift Detection in Brazilian Portuguese One-vs-Many Debates

This repository contains the official dataset (corpus) for the paper **"FALA20x1: Fallacy And Topic Drift Detection in Brazilian Portuguese One-vs-Many Debates"** submitted for peer review. 

> **Blind Review Notice:** This repository is entirely anonymized to comply with double-blind review policies. Author identities, institutional affiliations, and specific project links have been omitted or replaced with placeholders.

---

## 📌 Overview

**FALA20x1** is the first Brazilian Portuguese (PT-BR) oral discourse corpus annotated for argumentation fallacies and systematic topic drift under high-pressure scenarios. The dataset is built from transcriptions of the "20-versus-1" format from the Spectrum YouTube channel, capturing unique asymmetric group dynamics (1-vs-many) where a single proponent defends a thesis against a rotating panel of twenty opponents.

### Key Features
* **Spontaneous Orality:** Captures real-world conversational complexities such as interruptions, high-velocity argumentation, and pragmatic ambiguity.
* **Integrated Topic Drift Layer:** Elevates thematic deviation (`distanciamento`) into a primary analytical layer, establishing empirical correlations between logical flaws and conversational evasion.
* **Disjunctive Protocol:** Embraces linguistic subjectivity by supporting fallback disjunctive annotations (e.g., `[RDH or ESP]`) for highly ambiguous rhetorical maneuvers.

---

## 📊 Dataset Statistics & Structure

The repository includes the complete corpus partitioned across multiple thematic debate blocks (Excel files):
* **Total Duration:** 14 Hours
* **Total Debate Blocks:** 42 independent 20-minute rounds
* **Total Speech Turns:** 5,316 analyzed segments

### Data Schema
The corpus files are structured with the following 10 structural and analytical attributes:

| Column | Description |
| :--- | :--- |
| `id_turno` | Sequential identifier of the speech turn within the block. |
| `span_inicio` | Timestamp (seconds) indicating the start of the segment. |
| `span_fim` | Timestamp (seconds) indicating the end of the segment. |
| `Trecho` | Transcribed speech text in Brazilian Portuguese (PT-BR). |
| `falante` | Target role category: `SOLO` (lone proponent) or `GRUPO` (opponents). |
| `nivel0` | Binary structural indicator: `FALACIA` or left blank (Non-Fallacy). |
| `nivel1` | Hierarchy Level 1: Macro-categories (`LOGICA`, `CREDIBILIDADE`, or `EMOCAO`). |
| `nivel2` | Hierarchy Level 2: Specific short-codes for taxonomy tags (e.g., `[ADH]`, `[ESP]`). Supports single, multi-label, and disjunctive notations. |
| `distanciamento` | Level of conversational topic drift relative to the core thesis: `LEVE`, `MODERADO`, `GRAVE`, or `RETORNO`. |
| `justificativa` | Natural language explanation justifying the human annotation labels. |

---

## 🏷️ Hierarchy Taxonomy (Adapted from MAFALDA)

The annotation protocol maps three macro-categories into specific micro-level fallacies:

1. **Credibility (*Ethos*)**
   * `[ADH]` Ad Hominem Abusive
   * `[AFA]` Appeal to False Authority
   * `[TUQ]` Tu Quoque
   * `[CPA]` Guilt by Association
2. **Logic (*Logos*)**
   * `[ESP]` Straw Man
   * `[RDH]` Red Herring / Topic Drift
   * `[FDC]` False Dichotomy
   * `[ESC]` Slippery Slope
   * `[GEA]` Hasty Generalization
   * `[RCI]` Circular Reasoning
   * `[CAF]` False Causality
3. **Emotion (*Pathos*)**
   * `[APO]` Appeal to the People (*Ad Populum*)
   * `[ARD]` Appeal to Ridicule

---

## 🤖 Baseline Reference Results

For benchmarking context, the baseline evaluation experiments described in the paper achieved the following results on this corpus:

| Model / Approach | Logic ($F_1$) | Credibility ($F_1$) | Emotion ($F_1$) | Macro $F_1$ | Micro $F_1$ |
| :--- | :---: | :---: | :---: | :---: | :---: |
| TF-IDF + Linear SVM | 0.31 | 0.21 | 0.07 | 0.1982 | 0.2903 |
| **TF-IDF + Logistic Regression** | **0.38** | **0.28** | **0.08** | 0.2452 | **0.3348** |
| **BERTimbau Base (Weighted)** | 0.37 | **0.28** | **0.08** | **0.2460** | 0.3053 |

---

## 📂 How to Load the Data

The corpus blocks can be easily loaded and consolidated using Python and `pandas`:

```python
import glob
import os
import pandas as pd

# Path to the dataset directory
folder_path = "THE FALA20X1 CORPUS"
all_files = glob.glob(os.path.join(folder_path, "*.xlsx"))

dfs = []
for file in all_files:
    df = pd.read_excel(file)
    # Skip the metadata description row (row 2 in the Excel files)
    df = df.iloc[1:].reset_index(drop=True)
    df['debate_block'] = os.path.basename(file).replace('.xlsx', '')
    dfs.append(df)

# Final consolidated DataFrame
corpus_df = pd.concat(dfs, ignore_index=True)
print(f"Loaded {len(corpus_df)} speech turns.")
