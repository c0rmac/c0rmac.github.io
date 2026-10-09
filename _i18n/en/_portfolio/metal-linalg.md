**metal-linalg** provides QR decomposition, symmetric eigendecomposition (`eigh`) and the singular value decomposition (SVD) for batches of matrices on Apple Silicon GPUs. It works with [MLX](https://github.com/ml-explore/mlx) arrays, [PyTorch](https://pytorch.org) tensors and plain float buffers. It is written in C++, with Python packages for MLX and PyTorch, a Swift package and a C API.

MLX's own `eigh` and `svd` run only on the CPU, and PyTorch has no GPU kernels for most of these decompositions on a Mac. metal-linalg fills that gap.

[**View on GitHub**](https://github.com/c0rmac/metal-linalg) · [**Which Macs are measured**](https://c0rmac.github.io/metal-linalg/docs/measurements)

## ⚙️ How it works

Each solver has several Metal kernels, one per regime:

- **Large batches of small matrices:** LAPACK's own methods, one matrix per threadgroup or simdgroup (Householder QR, tridiagonalization and implicit QL for `eigh`, Golub–Kahan bidiagonalization for the SVD).
- **One large matrix:** the memory-bound reduction to tridiagonal or bidiagonal form runs on the GPU, while divide and conquer runs on every CPU core. For eigenvalues or singular values alone, a two-stage reduction goes to a band on the GPU, then to tridiagonal form on the CPU, with bisection on the GPU.
- **Long, thin matrices:** QR first, then the small square problem.

Which kernel is fastest, and where the GPU overtakes the CPU, depends on the chip. So every call is routed by a **policy measured on the Mac it runs on**, keyed on the Metal device name and GPU core count. A Mac nobody has measured gets an estimate, refitted from a measured Mac and published benchmarks.

## 📈 Performance

On an Apple M5 Pro, against a CPU path that spreads every call over all 18 cores:

| one N×N matrix | 2048 | 4096 |
|---|---|---|
| SVD, with vectors | 5.6x | **10.4x** |
| QR | 5.2x | **10.3x** |
| singular values alone | 3.8x | **9.95x** |
| eigh, with eigenvectors | 4.5x | **9.2x** |

Against `torch.linalg` with PyTorch 2.13:

| | torch, CPU | torch, MPS | metal-linalg-torch |
|---|---|---|---|
| QR, 1024 × 128×128 | 202 ms | 32 ms | **4.4 ms** |
| SVD, 4096 × 32×32 | 223 ms | 232 ms | **6.3 ms** |
| eigh, 4096 × 16×16 | 33 ms | 35 ms | **2.0 ms** |
| SVD, one 4096×4096 | 3.60 s | 3.64 s | **339 ms** |

## 💻 Installation

```bash
pip install metal-linalg          # for MLX
pip install metal-linalg-torch    # for PyTorch
```

```bash
brew tap c0rmac/metal-linalg
brew install metal-linalg         # the C++ library and C API
```

## ⚡️ Quick start

```python
import torch
import metal_linalg_torch as mlt

a = torch.randn(1000, 64, 32, device="mps")
Q, R = mlt.qr(a)                  # like torch.linalg.qr
U, S, Vh = mlt.svd(a)             # thin factors
L, V = mlt.eigh(a.mT @ a)         # like torch.linalg.eigh
```

The PyTorch functions mirror their `torch.linalg` namesakes, support autograd and compile with `torch.compile`.

## 🤝 Contributing

The routing is only as good as the measurements behind it, and every new chip needs its own. If you have an Apple Silicon Mac, one command (`python3 tuning/run.py`) measures it and produces a results folder to send as a pull request. See [how to contribute](https://github.com/c0rmac/metal-linalg/blob/main/CONTRIBUTING.md).
