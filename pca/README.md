# PCA Benchmark for Cardiac Motion

This folder contains the linear baseline used to benchmark *Cardio4D-VAE*. Principal component analysis (PCA) is applied to the same left ventricular (LV) mesh sequences used to train the VAE, and the first 32 principal components are analysed with the same latent–phenotype association framework, allowing a direct comparison between linear and nonlinear representations of cardiac motion.

## Workflow

### 1. PCA on motion data (`motion_deepmesh_pca.ipynb`)
- Load LV mesh sequences (50 frames × 1,200 vertices × 3 coordinates), generated with [DeepMesh](https://github.com/ImperialCollegeLondon/DeepMesh) and aligned to a common atlas.
- Apply global min–max scaling across all subjects, as for *Cardio4D-VAE*.
- Flatten each sequence to a single vector and fit Incremental PCA (32 components, batch size 1,000).
- Report explained and cumulative explained variance per component.
- Reconstruct held-out test subjects and compute reconstruction error.
- Save the fitted PCA model and per-subject principal component scores.

### 2. Association with conventional imaging traits (`pca_trait_associations.ipynb`)
The 32 principal components are analysed with the same models used for the *Cardio4D-VAE* latents:
- **Per-trait multivariable regression:** each imaging trait regressed on all 32 components, giving the variance explained ($R^2$).
- **Component–trait regression:** each trait regressed on one component at a time, giving effect sizes, *P* values and FDR-corrected significance (Benjamini–Hochberg).
- All models are adjusted for age at MRI, sex, ethnicity and body surface area; components and traits are standardised before regression.
- Outputs include volcano plots, effect-size heatmaps, a two-dimensional projection of component association profiles, and pairwise correlations between components.

## Requirements

- Python 3.8
- numpy, pandas, scikit-learn, statsmodels, scipy, matplotlib, seaborn, pyvista


## Code availability

This analysis was performed on individual-level UK Biobank data, which are subject to strict access restrictions. To comply with these restrictions, the code is not yet publicly available. 

