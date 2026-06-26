# Mitochondrial Gene-Based Machine Learning Classification of Long COVID Persistence

This repository contains the complete code, data, trained models, and figures associated with the manuscript:

**"Mitochondrial Dysfunction Signatures Predict Long COVID Persistence: A Multi-Cohort Machine Learning Approach Using Transcriptomic Data"**

---

## Overview

A Random Forest classifier trained on 149 mitochondrial gene expression features from six independent cohorts (n=343) distinguishes persistent Long COVID from recovered individuals with an AUC of 0.986. SHAP-based explainability analysis identifies cytochrome c oxidase (Complex IV) subunit genes (COX8C, COX6A2, COX7A1, COX4I2) as the strongest molecular predictors of disease persistence.

---

## Repository Structure

```
longcovid-mitochondrial-ml/
├── README.md
├── code/
│   ├── run_full_pipeline.py            # Main pipeline (nested CV, SHAP, robustness)
│   ├── 00_download_and_process_all.py  # Raw data download and preprocessing
│   ├── 01_prepare_binary_dataset.py    # Binary classification dataset preparation
│   ├── 02_train_model.py              # Model training script
│   ├── 03_differential_expression.py   # Differential expression analysis
│   ├── 04_visualization.py            # Visualization utilities
│   └── requirements.txt               # Python dependencies
├── data/
│   ├── preprocessed/
│   │   ├── preprocessed_longcovid_BINARY_343samples.zip.part_aa  # Part 1 (20 MB)
│   │   ├── preprocessed_longcovid_BINARY_343samples.zip.part_ab  # Part 2 (20 MB)
│   │   ├── preprocessed_longcovid_BINARY_343samples.zip.part_ac  # Part 3 (17 MB)
│   │   └── REASSEMBLE.md              # Instructions to reassemble the dataset
│   └── metadata/
│       ├── DATASET_INFO.txt                    # Dataset description
│       ├── y_metadata_binary.csv               # Sample labels and cohort assignments
│       └── mitochondrial_genes_in_dataset.csv  # 149 mitochondrial genes with categories
├── results/
│   ├── FULL_SUMMARY.json              # Complete results summary
│   ├── metrics/
│   │   ├── nested_cv_results.csv      # 5-fold nested CV metrics for all models
│   │   ├── shap_feature_importance.csv # SHAP-based gene importance ranking
│   │   ├── loso_cv_per_study.csv      # Leave-One-Study-Out CV results
│   │   ├── ablation_progressive.csv   # Progressive feature ablation
│   │   ├── ablation_single_removal.csv # Single feature removal impact
│   │   ├── permutation_test.json      # Permutation test results (p=0.005)
│   │   ├── data_leakage_detection.json # Data leakage control results
│   │   ├── feature_importance_model.csv
│   │   └── feature_importance_permutation.csv
│   ├── models/
│   │   ├── best_model_Random_Forest.joblib  # Trained Random Forest model
│   │   ├── scaler.joblib                    # Fitted StandardScaler
│   │   └── model_metadata.json             # Hyperparameters and configuration
│   ├── plot_data/
│   │   ├── roc_curves.json
│   │   ├── precision_recall_curves.json
│   │   ├── learning_curves.json
│   │   ├── hyperparameter_sensitivity.json
│   │   └── shap_values_matrix.csv.gz
│   ├── npy_arrays/                    # NumPy arrays for reproducible plotting
│   └── logs/
│       └── pipeline.log               # Full pipeline execution log
└── figures/
    ├── jpg/                           # Publication-quality JPG (600 DPI, RGB)
    │   ├── Figure_1.jpg               # Study workflow
    │   ├── Figure_2.jpg               # ROC, PR curves, confusion matrix
    │   ├── Figure_3.jpg               # LOSO-CV results
    │   ├── Figure_4.jpg               # SHAP feature importance
    │   └── Figure_5.jpg               # Biological interpretation
    └── scripts/                       # Python scripts to regenerate figures
```

---

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/longcovid-mitochondrial-ml.git
cd longcovid-mitochondrial-ml
```

### 2. Reassemble the preprocessed dataset

The dataset was split into parts due to GitHub file size limitations.

```bash
cd data/preprocessed
cat preprocessed_longcovid_BINARY_343samples.zip.part_* > preprocessed_longcovid_BINARY_343samples.zip
unzip preprocessed_longcovid_BINARY_343samples.zip
cd ../..
```

### 3. Install dependencies and run

```bash
pip install -r code/requirements.txt
python code/run_full_pipeline.py
```

All analyses use `random_state=42` for reproducibility.

---

## Key Results

| Metric | Value |
|--------|-------|
| Best Model | Random Forest |
| AUC (Nested 5-Fold CV) | 0.986 +/- 0.015 |
| F1-Score | 0.932 +/- 0.033 |
| Accuracy | 0.939 +/- 0.029 |
| Permutation Test p-value | 0.005 |
| Train-Test Gap | 0.0103 |
| Gap Nested CV AUC | 0.912 |
| Top Biomarker | COX8C (SHAP = 0.0555) |

---

## Data Availability

Raw RNA-seq data are publicly available from NCBI Gene Expression Omnibus (GEO):

| Accession | Platform | Samples | Type | Link |
|-----------|----------|---------|------|------|
| GSE267625 | Bulk RNA-seq (Illumina NovaSeq 6000) | 111 | Persistent | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE267625](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE267625) |
| GSE169687 | Bulk RNA-seq (Illumina NovaSeq 6000) | 142 | Recovered/Healthy | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE169687](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE169687) |
| GSE152418 | Bulk RNA-seq (Illumina NovaSeq 6000) | 34 | Recovered/Healthy | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE152418](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE152418) |
| GSE222253 | Bulk RNA-seq (Illumina NovaSeq 6000) | 35 | Persistent | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE222253](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE222253) |
| GSE265753 | scRNA-seq (BD Rhapsody) | 18 | Mixed | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE265753](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE265753) |
| GSE235938 | scRNA-seq (10x Genomics Chromium) | 3 | Mixed | [https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE235938](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE235938) |

The mitochondrial gene set was derived from the MitoCarta3.0 database:
- [https://www.broadinstitute.org/mitocarta/mitocarta30-inventory-mammalian-mitochondrial-proteins-and-pathways](https://www.broadinstitute.org/mitocarta/mitocarta30-inventory-mammalian-mitochondrial-proteins-and-pathways)

The preprocessed dataset (149 mitochondrial genes x 343 samples) is included in this repository under `data/preprocessed/` (split into parts; see REASSEMBLE.md for instructions).

---

## Figures

All figures are formatted for journal submission (Computers in Biology and Medicine, Elsevier):
- Resolution: 600 DPI
- Color mode: RGB
- Full page width: minimum 3740 pixels

Figure generation scripts are provided in `figures/scripts/` for full reproducibility. Running these scripts will produce TIFF, PNG, and JPG outputs as needed.

---

## Requirements

```
Python 3.11
scikit-learn 1.3
xgboost 2.0
shap 0.42
pandas 2.0
numpy 1.24
scipy 1.11
matplotlib 3.7
seaborn 0.12
joblib
```

---

## Citation

If you use this code or data, please cite:

> [Manuscript citation to be added upon publication]

---

## License

This project is provided for academic and research purposes. Please contact the authors for commercial use.
