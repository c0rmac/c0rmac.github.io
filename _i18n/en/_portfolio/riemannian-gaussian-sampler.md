**Riemannian Gaussian Sampler** is a C++ library for exact, efficient sampling from isotropic Gaussian distributions on compact matrix manifolds. It currently supports the **rotation group** $SO(d)$ and the **Stiefel manifold** $V(n, k)$.

Samples lie exactly on the manifold (up to floating-point precision) with the correct concentration around a given mean. The spectral approach avoids both the exponentially poor acceptance rates of rejection sampling and the discretisation error of geodesic random walks, and it is fast enough for large batches on the CPU and the GPU.

[**View on GitHub**](https://github.com/c0rmac/riemannian-gaussian-sampler)

## 📐 The idea

The Riemannian Gaussian with concentration $\alpha > 0$ around a mean $\widehat{M}$ has density

<div>$$\mu_\infty(X) \propto \exp\left(-\alpha\, d_g^2(X,\, \widehat{M})\right)$$</div>

with respect to the Riemannian volume, where $d_g$ is the geodesic distance. Its normalising constant has no closed form, and the manifold is high-dimensional.

The key is to split every point into a low-dimensional **shape** and a high-dimensional but tractable **orientation**:

<div>$$X = h \cdot \exp(A(\theta)) \cdot \widehat{M}, \qquad h \in H,\quad \theta \in \mathcal{W},$$</div>

where $H$ is a stabiliser subgroup and $\mathcal{W}$ is the Weyl chamber, of dimension $\lfloor d/2 \rfloor$ for $SO(d)$ and $k$ for $V(n, k)$. The Riemannian volume factorises exactly over this split, so the sampler:

1. **Phase I** (once): draws the shape parameters $\theta$ by Hamiltonian Monte Carlo on the low-dimensional Weyl chamber;
2. **Phase II** (per sample): draws a Haar-random orientation $h$ and lifts the sample back to the manifold.

No approximation is made: the samples are exact draws from $\mu_\infty$. A paper with full proofs is under review.

## 💻 Installation

Built on [Isomorphism](/portfolio/isomorphism/), so it runs on MLX, LibTorch or Eigen:

```bash
brew tap c0rmac/homebrew-isomorphism
brew tap c0rmac/homebrew-riemannian-gaussian-sampler
brew install c0rmac/homebrew-riemannian-gaussian-sampler/riemannian-gaussian-sampler-mlx
```

## ⚡️ Quick example

```cpp
#include <sampler/isotropic/so_gaussian_sampler.hpp>
#include <isomorphism/math.hpp>

namespace math = isomorphism::math;

int main() {
    const int d = 4;
    isomorphism::Tensor M_hat = math::eye(d, isomorphism::DType::Float32);

    sampler::SOdGaussianSampler::Config cfg;
    cfg.alpha       = 2.0;   // higher = tighter around M_hat
    cfg.num_samples = 1;

    sampler::SOdGaussianSampler samp(M_hat, d, cfg);
    isomorphism::Tensor X = samp.sample();   // [1, d, d]
}
```
