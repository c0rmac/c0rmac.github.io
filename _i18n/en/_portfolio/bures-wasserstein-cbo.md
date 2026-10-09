**Gaussian CBO on Bures–Wasserstein space** runs consensus-based optimisation (CBO) for Gaussian variational inference directly on the Bures–Wasserstein manifold, with no reference measure, no linearisation and no regularisation of the objective.

[**View on GitHub**](https://github.com/c0rmac/bures-wasserstein-cbo)

## 🎯 The problem

Borghi & Carrillo (ICML 2026) brought CBO to Gaussian variational inference: a swarm of Gaussian particles, each with a mean $m$ and covariance $\Sigma$, searches for the best Gaussian approximation of a target. To keep the objective finite near singular covariances, they work in a linearisation of the Bures–Wasserstein manifold and introduce eigenvalue clipping, a regularised entropy and a penalty value for singular particles. Their outlook asks whether the dynamics can be run in the full Bures–Wasserstein space instead.

## 💡 The idea

The Bures–Wasserstein boundary is only the singularity of $a \mapsto a^2$ at the origin. Writing each particle through a **square root** $\Sigma = AA^\top$ removes it: the flat metric on square roots induces the Bures–Wasserstein metric exactly, and singular Gaussians become an ordinary subvariety rather than a boundary.

The tangent space of the square roots splits into a **horizontal** part, which moves the Gaussian, and a **vertical** part, which does not. Ambient noise wastes about half its budget on the vertical part. The horizontal space is exactly the set of matrices $SA$ with $S$ symmetric, so noise generated as $SA$ is horizontal by construction, with no projection and no eigenvalue denominators. Weighting its coordinates recovers Borghi & Carrillo's anisotropic noise in full.

As a result:

- each Euler step is a congruence, so the rank of $\Sigma$ is preserved and the boundary is repelled;
- the coefficients stay finite at rank-deficient covariances;
- no regularisation length needs to be chosen by hand.

## 📈 Results

On Borghi & Carrillo's released $d = 10$ benchmark (fitting a Gaussian to a 5-component mixture, 30 instances, 100 particles):

| | covariance error | $W_2$ distance | runs within $W_2$ of 0.1 |
|---|---|---|---|
| Borghi & Carrillo | 19% | 0.31 | 0 of 30 |
| **Horizontal anisotropy** | **4.6%** | **0.094** | **19 of 30** |

The repository contains the [full proposal](https://github.com/c0rmac/bures-wasserstein-cbo/blob/main/proposal-horizontal-purification.md) and the [convergence report](https://github.com/c0rmac/bures-wasserstein-cbo/blob/main/ha-convergence-report.md).
