# Unsupervised Clustering of Cardiac Motion 

This folder contains the unsupervised tree-based clustering of left ventricular (LV) motion. The 32-dimensional latent motion features learned by *Cardio4D-VAE* are mapped onto a two-dimensional tree-structured manifold, organising individuals along continuous phenotypic paths rather than discrete diagnostic categories.

## Method overview

### 1. Morion Taxonomy
- Discriminative Dimensionality Reduction via Learning a Tree jointly learns a low-dimensional embedding and a principal tree, mapping each participant's latent vector $\mathbf{z}_i \in \mathbb{R}^{32}$ to two coordinates ($c_1$, $c_2$).
- Implemented with the R package *Monocle*.
- The model was trained on 36,856 participants and applied to 41,257 unseen participants to assess generalisation.

### 2. Trajectory and branch definition
- The root is defined as the subject closest to the global mean in the embedding space.
- A minimum spanning tree is built from the root, with each branch running from the root to a terminal node.
- Each participant is assigned a pseudotime value describing their position along a branch.
- The tree identifies five main branches, two of which subdivide, giving seven phenotypic paths.

### 3. Stability and generalisation
- **Stability:** for each tested value of $\gamma$ (2, 5, 10), 500 replicates were generated on random 90% subsamples, and the median preservation of the 50 nearest neighbours was computed. $\gamma = 5$ was chosen as the best balance between overfitting and stability.
- **Generalisation:** tree structure and coordinate distributions were compared between training and unseen participants (trustworthiness, Wasserstein distance, Jensen–Shannon divergence).

### 4. Mapping traits onto the tree
- **Latent features:** changes in each latent feature along pseudotime are tested between the start and end of each lineage (*tradeSeq*).
- **Phenotypes:** continuous traits (e.g. blood pressure, HbA1c, LV mass, LVEDVi, wall thickness, diastolic strain rate) are modelled across the embedding with generalised additive models (GAMs).
- **Genotypes:** the probability of carrying rare variants (*TTN* truncating variants, HCM P/LP variants) is mapped onto the tree.
- **Outcomes:** 5-year risk of major adverse cardiovascular events (MACE) is estimated per branch using Cox proportional hazards models, and the prognostic value of $c_1$ and $c_2$ is compared with conventional imaging markers (LVEF, LVEDVi, global longitudinal strain).


## Code availability

The scripts for this analysis were run on the UK Biobank Research Analysis Platform (RAP) and will be released here once access to the RAP is restored.

## Requirements

- R with *Monocle*, *DDRTree*, *tradeSeq*, *mgcv* and *survival*