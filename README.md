# Auditing label conflicts, evaluation protocols, and data splitting in deep learning for oral cancer histopathology

Supplementary data and code for the article:

> F. M. Amien, C. Fatichah, and A. Yuniarti, "Auditing Label Conflicts, Evaluation Protocols, and Data Splitting in Deep Learning for Oral Cancer Histopathology," *Indonesian Journal of Electrical Engineering and Informatics (IJEEI)*, submitted.

The study uses version 2 of the public *Histopathological imaging database for oral cancer analysis* by Rahman et al. (Mendeley Data, doi: [10.17632/ftmp4cvtmb.2](https://doi.org/10.17632/ftmp4cvtmb.2)). The images are **not** redistributed here; download them from the original source.

## Contents

| Path | Description |
|---|---|
| `data/S1_test_results_108.csv` | All 108 test results (12 models × 3 domains × 3 partitioning schemes): accuracy, macro F1-score, AUC-ROC, Cohen's kappa, per-class recall, and confusion matrix (TN, FP, FN, TP). Rank is by macro F1-score, ties broken by AUC. For 5-fold CV, macro F1 is the mean over folds and the confusion matrix is pooled over folds. |
| `data/S2_literature_audit.csv` | Protocol audit of the 31 studies: reference number in the article, study, DOI, audit outcome, first decisive criterion (C1–C5), and an evidence note. |
| `data/S3_duplicate_pairs.csv` | The 16 byte-identical image pairs found by MD5 digest: 14 with conflicting labels (3 at 100×, 11 at 400×) and 2 with the same label. File names are as in the dataset folders. |
| `data/Supplementary_Material.xlsx` | The same three tables in one workbook. |
| `code/analysis.py` | Reproduces the statistics reported in the article from `S1`: Friedman and Wilcoxon–Holm tests, Spearman's ρ and top-3 overlap, cross-validated rank of each hold-out winner, measurement resolution, Wilson 95% intervals, and the null simulation of the winner's curse. |
| `code/md5_duplicates.py` | Finds byte-identical images in a local copy of the dataset and writes the duplicate list. |

## Audit criteria

A study was considered directly comparable if it met all five criteria:

- **C1** Use of the Rahman et al. dataset verifiable from the full text.
- **C2** Ratio or number of images reported for every subset.
- **C3** No augmentation, oversampling, or cropping before splitting (or a separately reported result without such steps).
- **C4** No selection of models, ensemble weights, thresholds, or hyperparameters on the test set.
- **C5** Evaluation at a stated magnification matching the comparison (100×, 400×, or combined).

Candidate studies were identified on 21 September 2026 through Google Scholar as works citing the data article or the dataset; three of 34 candidates were excluded because their bibliographic records could not be verified, leaving 31.

## Usage

```bash
pip install -r requirements.txt
python code/analysis.py
python code/md5_duplicates.py "/path/to/Histopathological imaging database for Oral Cancer analysis" --out duplicates.csv
```

Note on the null simulation: the article reports Monte Carlo estimates from 20,000 replicates. `analysis.py` also prints the exact expectation; the two agree to within 0.05 points (for example, 6.25 points exact for the 80:10:10 split at 400×).

## Training protocol (summary)

ImageNet-pretrained models with only the final layer replaced by a two-class output; images resized to 224×224 and normalized with ImageNet statistics; on-the-fly augmentation of training images only, after partitioning; AdamW (learning rate 1e-4 for pretrained layers, 1e-3 for the new layer, weight decay 1e-2), cosine annealing with a two-epoch warm-up and frozen backbone, class-weighted cross-entropy with label smoothing 0.1, batch size 16, mixed precision, at most 20 epochs with early stopping (patience 5) on validation loss; seed 42.

## License

Data (`data/`) are released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/); code (`code/`) under the MIT License (see `LICENSE`).

## Contact

Chastine Fatichah (corresponding author), Department of Informatics, Institut Teknologi Sepuluh Nopember, Surabaya, Indonesia — chastine@its.ac.id
