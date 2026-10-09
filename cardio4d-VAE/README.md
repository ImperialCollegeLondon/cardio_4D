# Spatiotemporal Variational Autoencoder for Cardiac Motion (Cardio4D-VAE)

Cardio4D-VAE is a spatiotemporal β-VAE developed to learn compact, generative representations of 4D cardiac motion from dynamic left ventricular (LV) mesh sequences.
Trained in an unsupervised setting on UK Biobank participants, the model encodes each cardiac cycle (50 frames × 1,200 vertices × 3 coordinates) into a $d$-dimensional latent space ($d = 32$) capturing both anatomical shape and temporal deformation patterns.

## Workflow

### 1. Preprocessing
- Extract LV mesh sequences (e.g., using [DeepMesh](https://github.com/ImperialCollegeLondon/DeepMesh)).
- Spatially align each subject's LV mesh to a common reference: the end-diastolic frame is rigidly registered to the atlas and the transform is applied to all frames.
- Down-sample meshes to 1,200 vertices using quadric error decimation.
- Apply dataset-level min–max normalisation, computed on the training set and applied unchanged to all other subjects.

### 2. Training (`train_vae.py`)
- Train Cardio4D-VAE to learn a probabilistic $d$-dimensional latent representation.
- The β-VAE objective encourages disentangled and interpretable latent factors.
- Periodically saves model checkpoints and latent embeddings.
- Model development uses randomly selected participants.

### 3. Evaluation (`test_vae.py`)
- Load a trained model and evaluate reconstruction quality (RMSE in mm).
- Extract per-subject latent vectors $\mathbf{z}_i \in \mathbb{R}^d$.
- Generate reconstructed or synthetic LV motion sequences.



---

## Model and training settings

| Setting | Value |
|---|---|
| Encoder | ResNet-3D-18 backbone |
| Decoder | Fully connected (hidden layer 1,024, ReLU, sigmoid output) |
| Latent dimensionality ($d$) | 32 |
| Optimiser | Adam |
| Learning rate | $10^{-4}$ |
| Batch size | 32 |
| Epochs | 30 |

### Loss weights

Selected by Bayesian hyperparameter optimisation with Optuna (`optimisation_visulisation.ipynb`). Weights were sampled log-uniformly in $[10^{-5}, 1]$, $d$ was selected from $\{16, 32, 64, 128\}$, and the search was repeated across five random seeds (1701, 1993, 2023, 42, 1994).

| Loss term | Weight | Argument |
|---|---|---|
| KL divergence ($w_{\mathrm{KL}}$) | $2 \times 10^{-5}$ | `--beta` |
| Laplacian smoothing ($w_{\mathrm{lap}}$) | $10^{-4}$ | `--alpha` |
| Normal consistency ($w_{\mathrm{norm}}$) | $10^{-4}$ | `--gamma` |
| Temporal smoothness ($w_{\mathrm{temp}}$) | $10^{-4}$ | `--delta` |

## Usage

```bash
# Training
python train_vae.py \
    --input_dir <data_dir> --output_base_dir <output_dir> --atlas <atlas.vtk> \
    --encoded_dim 32 --epochs 30 --batch_size 32 --lr 1e-4 \
    --beta 2e-5 --alpha 1e-4 --gamma 1e-4 --delta 1e-4 \
    --device cuda:0

# Evaluation and latent extraction
python test_vae.py \
    --input_dir <data_dir> --output_base_dir <output_dir> --atlas <atlas.vtk> \
    --encoded_dim 32 --device cuda:0
```

## Requirements

- Python 3.8.10
- PyTorch 1.13.0 (CUDA 11.7), torchvision
- [PyTorch3D](https://github.com/facebookresearch/pytorch3d)
- PyTorch Geometric 2.5.3
- Optuna 4.0.0
- numpy, pandas, scikit-learn, statsmodels, matplotlib, seaborn, pyvista



