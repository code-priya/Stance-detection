## Overview

This project focuses on **three-class climate stance detection** using transformer-based language models. A unified English dataset is created by combining two publicly available benchmark datasets. Multiple transformer architectures are trained and compared, followed by cross-lingual evaluation on Indian language social media posts using machine translation.

---

# Dataset

## Source Datasets

| Dataset | Source | Samples |
|----------|--------|---------|
| GWSD (Global Warming Stance Dataset) | https://github.com/yiweiluo/GWStance/blob/master/GWSD.tsv | ~2,300 |
| SemEval-2016 Task 6 | https://alt.qcri.org/semeval2016/task6/data/uploads/semeval2016-task6-trainingdata.txt | ~2,800 |

## Data Cleaning

Both datasets were cleaned and converted into a common format.

### Final Columns

| Column | Description |
|---------|-------------|
| `text` | Social media post |
| `label` | Stance label |

### Labels

- Favor
- Against
- Neutral

### Cleaning Steps

- Removed empty rows
- Removed duplicate samples
- Removed invalid records
- Merged both datasets

---

# Final Dataset

| Property | Value |
|----------|-------|
| Total Samples | ~4,850 |
| Classes | 3 |
| Language | English |


# Dataset Structure

The project is organized as follows:

```text
data/
├── dataset-details/        # Information about each dataset (source, number of samples, label distribution, etc.)
├── test/                   # Unlabelled test datasets
│   ├── Bangla_500_test data.xlsx
│   └── Hindi_500_test data.xlsx
├── test-with-labels/       # LLM-labelled test datasets
│   ├── Bangla_500_labeled.csv
│   └── Hindi_500_labeled.csv
├── train/                  
│   ├── GWStance_final.csv
│   ├── SemEval2016_final.csv
│   ├── train.csv
│   ├── valid.csv
│   └── test.csv
└── translated/             
# Machine-translated training datasets
    ├── merged_english.csv
    ├── merged_bengali.csv
    └── merged_hindi.csv
```

---

# Training Dataset Preparation

The original English climate stance datasets (GWStance and SemEval2016) were cleaned, merged, and processed to obtain a unified dataset containing approximately **4,800 samples**.

The processed dataset was split into:

- **Training set**
- **Validation set**
- **Test set**

These files are available in:

```
data/train/
```

---

# Bengali and Hindi Translation Pipeline

The cleaned and merged English climate stance dataset (~4,800 samples) was first translated into **Bengali** using a pretrained neural machine translation model(`facebook/nllb-200-distilled-600M`). The translated dataset was saved as:

- `merged_bengali.csv`

The same English dataset was then translated into **Hindi** using a pretrained neural machine translation model(`facebook/nllb-200-distilled-600M`). The translated dataset was saved as:

- `merged_hindi.csv`

The original English dataset was also retained as:

- `merged_english.csv`

All translated datasets are available in:

```
data/translated/
```

These translated datasets were subsequently used for multilingual model training and evaluation.

---

# Test Dataset

The project includes an **unlabelled multilingual test dataset** consisting of:

- **500 Bengali comments**
- **500 Hindi comments**

These files are located in:

```
data/test/
```

---

# Test Dataset Annotation

The unlabelled Bengali and Hindi test datasets were annotated using a **Large Language Model (LLM) few shot**(`Claude Sonnet`) to assign one of the following stance labels:

- **Favor**
- **Against**
- **Neutral**

The resulting labelled datasets are available in:

```
data/test-with-labels/
```

To provide a reliable evaluation while reducing manual annotation effort, **10% of the LLM-labelled samples** were selected as the final evaluation set for testing the trained models.

---

# Dataset Details

Additional information about the datasets, including their sources, preprocessing steps, number of samples, and label distribution, is provided in:

```
data/dataset-details/
```
# Dataset Preparation

The original translated datasets (`merged_bengali.csv` and `merged_hindi.csv`) were first combined with **20% (100 samples)** from their corresponding manually labelled test datasets (`Bangla_500_labeled.csv` and `Hindi_500_labeled.csv`). Duplicate text entries were removed to avoid data leakage.

The merged datasets were then split into **80% training**, **10% validation**, and **10% testing** using **stratified sampling** to preserve the original class distribution across the three stance labels (*Favor*, *Against*, and *None*).

The resulting datasets are organized as follows:

```
train-bengali/
├── train_bengali.csv
├── valid_bengali.csv
└── test_bengali.csv

train-hindi/
├── train_hindi.csv
├── valid_hindi.csv
└── test_hindi.csv
```

These datasets were used for training, hyperparameter tuning, and final evaluation of the climate stance detection models for Bengali and Hindi.

---

# Modeling

## Baseline Model(On original dataset)

- **BERT Small**

Current Result:

| Metric | Score |
|---------|------:|
| Test Macro F1 | **0.64** |

---

# Model Performance

The table below summarizes the Macro F1-score obtained by the models on the Hindi and Bengali climate stance detection datasets.

| Dataset  | Model | Macro F1-Score |
|----------|-------|---------------:|
| Hindi    | MuRIL (`google/muril-base-cased`) | **0.59** |
| Bengali  | IndicBERT (`ai4bharat/IndicBERTv2-MLM-only`) | **0.61** |

### PLANNED - `xlm-roberta-large, microsoft/mdeberta-v3-base`


# Evaluation Metrics

The following metrics will be reported for every experiment:

- Accuracy
- Precision
- Recall
- Macro F1 Score
- Weighted F1 Score
- Confusion Matrix
- Per-class Precision, Recall and F1

---

# Cross-Lingual Evaluation

## External Test Dataset

Approximately **1000 multilingual social media posts**.

| Language | Samples |
|-----------|---------|
| Hindi | 500 |
| Other Indian Languages | 500 |
| Total | 1000 |


---

# Evaluation Pipeline

```text
Indian Language Posts
          │
          ▼
Machine Translation
          │
          ▼
English Text
          │
          ▼
Transformer Model
          │
          ▼
Predicted Label
          │
          ▼
LLM Verification
          │
          ▼
Final Evaluation
```

---

# Translation Models

Candidate translation models:

- IndicTrans2
- NLLB-200
- M2M100
- MADLAD-400

---

# LLM Verification

Predictions generated by transformer models will be compared against modern LLMs.

Candidate LLMs:

- GPT
- Gemini
- Llama 4
- Claude
- Qwen

Evaluation settings:

- Zero-shot
- Few-shot

Agreement between LLMs and transformer models will also be analyzed.

---

