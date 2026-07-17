# Supervised cell-type classification from single-cell RNA-seq

Predicts immune cell type from expression via annotation tools (CellTypist, SingleR).

## Data
700 human PBMCs, 765 variable genes, 10 annotated
cell types (`scanpy.datasets.pbmc68k_reduced`). Heavy class
imbalance: 240 cells in the largest class, 8 in the smallest.

## Approach
- Stratified 75/25 train/test split; test set held out until final evaluation
- Compared logistic regression, random forest, gradient boosting
  by 5-fold stratified cross-validation *within the training set*
- Metric: macro F1 (I do not use accuracy because it is misleading under imbalance as a
  majority-class dummy scores 0.34 accuracy but 0.05 macro F1)

## Results
| model | CV macro F1 |:
| Logistic regression | 0.681 ± 0.025 |
| Gradient boosting | 0.647 ± 0.046 |
| Random forest | 0.641 ± 0.032 |

Held-out test: **0.646 macro F1 / 0.829 accuracy** (n=175).

## Interpretation
- Top weights recover canonical markers (CD79A, CD79B, MS4A1 for
  B cells), the model learned biology.
- Errors are confined to T-cell subsets. Merging them into one
  lineage label raises macro F1 to **0.900** with no model change:
  These labels derive from surface proteins (CD25/CD45RA/CD45RO) that RNA
  cannot fully resolve. Part of the increase is also a small-n relief (went from 10 to 6 classes)
- Rare classes (n=2, n=5 in test) are unmeasurable; one cell moves
  recall by 50 points yet control 20% of macro F1. Reported
  alongside per-class support for transparency.

## Reproducibility
All random seeds fixed. Preprocessing is fit inside CV folds to
prevent leakage. Run top-to-bottom in Colab; no GPU needed.
