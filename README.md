# Mitochondrial Gene Expression Predicts Long COVID Persistence: A Machine Learning Approach

This repository contains the complete analysis pipeline, trained models, preprocessed data, and publication-quality figures supporting the manuscript:

> **Mitochondrial Gene Expression Profile in Peripheral Blood Distinguishes Persistent Long COVID from Recovery: An XGBoost-Based Machine Learning Study with Independent External Validation**

---

## Overview

Long COVID is a multisystem syndrome persisting weeks to months after SARS-CoV-2 infection. This study demonstrates that mitochondrial gene expression profiles in peripheral blood can distinguish persistent Long COVID from recovery with high accuracy using machine learning.

Six independent GEO cohorts (n=343) were integrated, and an XGBoost classifier trained on 149 mitochondrial genes (MitoCarta3.0) achieved:

- **Nested CV AUC:** 0.999 +/- 0.001
- **External Validation AUC:** 0.984 (95% CI: 0.959-1.000)
- **Specificity:** 100% (zero false positives)
- **Sensitivity:** 60% at the CV-derived threshold (0.087)
- **Permutation test:** p = 0.0099

---

## Key Results

### Nested 5-Fold Cross-Validation

| Model | AUC-ROC | F1-Score | Sensitivity | Specificity | MCC |
|-------|---------|----------|-------------|-------------|-----|
| **XGBoost** | **0.999 +/- 0.001** | **0.976** | **0.977** | **0.979** | **0.958** |
| Random Forest | 0.999 +/- 0.002 | 0.981 | 0.985 | 0.979 | 0.964 |
| SVM | 0.995 +/- 0.005 | 0.981 | 0.985 | 0.979 | 0.964 |
| Logistic Regression | 0.991 +/- 0.012 | 0.968 | 0.962 | 0.979 | 0.943 |
| Gradient Boosting | 0.994 +/- 0.007 | 0.956 | 0.946 | 0.972 | 0.921 |

### External Validation (Threshold = 0.087)

| Metric | Value |
|--------|-------|
| AUC-ROC | 0.984 |
| 95% Bootstrap CI | 0.959-1.000 |
| Specificity | 1.000 |
| Sensitivity | 0.600 |
| MCC | 0.652 |

### Top 10 Predictive Genes (Gini Importance)

| Rank | Gene | Importance | Function |
|------|------|-----------|----------|
| 1 | TXNRD2 | 0.459 | Thioredoxin reductase (redox defense) |
| 2 | NDUFA13 | 0.157 | Complex I subunit |
| 3 | NDUFS7 | 0.126 | Complex I subunit |
| 4 | ATP5F1D | 0.094 | Complex V (ATP synthase) |
| 5 | NDUFB1 | 0.025 | Complex I subunit |
| 6 | NDUFB6 | 0.016 | Complex I subunit |
| 7 | NDUFS8 | 0.013 | Complex I subunit |
| 8 | NDUFB10 | 0.012 | Complex I subunit |
| 9 | PPARGC1B | 0.012 | Mitochondrial biogenesis regulator |
| 10 | SDHA | 0.011 | Complex II subunit |

---

## Repository Structure

```
longcovid-mitochondrial-ml/
├── README.md
├── code/
│   ├── full_pipeline_v4.py                # Main pipeline (nested CV + external validation)
│   ├── threshold_optimization.py          # Youden's J threshold optimization
│   ├── generate_figures.py                # Figure generation script
│   └── cohort_cv_pca_umap_analysis.py     # PCA/UMAP and partial cohort CV
├── dataset/
│   ├── DATASET_INFO.txt                   # Dataset description
│   ├── y_metadata_binary.csv              # Sample metadata and labels
│   └── mitochondrial_genes_in_dataset.csv # Gene annotations with categories
├── results/
│   ├── metrics/
│   │   ├── full_results.json              # Complete results summary
│   │   ├── nested_cv_results.csv          # Per-model nested CV results
│   │   ├── ext_val_predictions.csv        # Per-sample external predictions
│   │   ├── feature_importance_gini.csv    # Gini importance (all genes)
│   │   ├── feature_importance_permutation.csv
│   │   ├── ablation_progressive.csv       # Feature ablation analysis
│   │   └── loso_cv_results.csv            # Leave-one-study-out CV
│   ├── models/
│   │   ├── best_model_XGBoost.joblib      # Trained model
│   │   └── scaler.joblib                  # StandardScaler
│   └── npy_arrays/                        # NumPy arrays for ROC curves and predictions
├── figures/
│   ├── Figure1.tiff through Figure11.tiff # Publication-quality figures (300 DPI)
└── supplementary_figures/
    ├── Supplementary_Figure_S1_PCA_UMAP.png
    └── Supplementary_Figure_S2_Partial_Cohort_CV.png
```

---

## Data Sources

| Cohort | Samples | Type | Role |
|--------|---------|------|------|
| GSE267625 | 111 | Bulk RNA-seq, whole blood | Training |
| GSE169687 | 142 | Bulk RNA-seq, whole blood | Training |
| GSE265753 | 18 | scRNA-seq pseudobulk | Training |
| GSE235938 | 3 | scRNA-seq pseudobulk | Training |
| GSE222253 | 35 | Bulk RNA-seq, whole blood | External validation |
| GSE152418 | 34 | Bulk RNA-seq, PBMC | External validation |

Raw data are publicly available in the NCBI Gene Expression Omnibus (GEO) under the accession numbers listed above. Mitochondrial gene annotations were obtained from MitoCarta3.0 (Broad Institute).

---

## Reproducibility

### Requirements

```
python >= 3.9
numpy >= 1.24
pandas >= 2.0
scikit-learn >= 1.3
xgboost >= 2.0
matplotlib >= 3.7
seaborn >= 0.12
joblib >= 1.3
umap-learn >= 0.5
```

### Installation

```bash
pip install numpy pandas scikit-learn xgboost matplotlib seaborn joblib umap-learn
```

### Running the Pipeline

```bash
# Main analysis pipeline
python code/full_pipeline_v4.py

# Threshold optimization
python code/threshold_optimization.py

# Generate publication figures
python code/generate_figures.py

# PCA/UMAP and partial cohort cross-validation
python code/cohort_cv_pca_umap_analysis.py
```

---

## Methodology

### Classification Framework

- **Task:** Binary classification (Persistent vs. Recovered/Healthy)
- **Features:** 149 mitochondrial genes from MitoCarta3.0
- **Validation:** 5-fold nested cross-validation with held-out external cohorts
- **Threshold:** Youden's J index from cross-validation predictions (0.087)

### Robustness Controls

| Test | Result | Interpretation |
|------|--------|---------------|
| Permutation test (n=100) | p = 0.0099 | Model captures genuine biological signal |
| Train-CV gap | 0.0011 | Minimal overfitting |
| Feature ablation | Plateau at 10-20 genes | Compact gene subset sufficient |
| Bootstrap CI (n=2000) | 0.959-1.000 | Stable external performance |
| Partial cohort CV | AUC = 0.998 +/- 0.004 | Cross-cohort generalization confirmed |

### Normalization

- Training data: StandardScaler fit on pooled training samples
- External validation: Transformed using training scaler parameters only
- No data leakage: External data statistics never influence training

---

## Citation

If you use this code or data, please cite:

```
Firat Y. Mitochondrial Gene Expression Profile in Peripheral Blood Distinguishes
Persistent Long COVID from Recovery: An XGBoost-Based Machine Learning Study with
Independent External Validation. [Journal]. 2026.
```

---

## License

MIT License

---

## Contact

Yelda Firat - [GitHub](https://github.com/yeldafrt)
