**Isomorphism** is a hardware-agnostic C++ tensor math library with a pluggable backend architecture. Write your mathematical logic once with a unified API, and run it on Apple Silicon, the CPU or the GPU by choosing a backend at compile time.

The public API is fully decoupled from the backend (the Pimpl pattern), so switching from MLX to PyTorch is a one-word change in `target_link_libraries`, with no changes to application code. It is the foundation for my manifold optimisation libraries, [Involute](/portfolio/1-involute/) and the [Riemannian Gaussian Sampler](/portfolio/riemannian-gaussian-sampler/).

[**View on GitHub**](https://github.com/c0rmac/isomorphism)

## 🔌 Backends

| Backend | Hardware | Dependency |
|---|---|---|
| **MLX** | Apple Silicon (Metal GPU) | [MLX](https://github.com/ml-explore/mlx), with `qr`, `eigh` and `svd` on the GPU through [metal-linalg](/portfolio/metal-linalg/) |
| **Eigen** | CPU (any platform) | [Eigen3](https://eigen.tuxfamily.org) |
| **Torch** | CPU / CUDA / MPS | [LibTorch](https://pytorch.org) |

A SYCL / oneMKL backend for PC GPUs is in progress.

## 💻 Installation

Each backend is a separate Homebrew formula:

```bash
brew tap c0rmac/homebrew-isomorphism
brew install isomorphism-mlx      # or isomorphism-eigen, isomorphism-torch
```

Then in your `CMakeLists.txt`:

```cmake
find_package(isomorphism REQUIRED)
target_link_libraries(my_app PRIVATE isomorphism::mlx)    # or ::eigen, ::torch
```

## ⚡️ Quick example

```cpp
#include <isomorphism/math.hpp>
#include <isomorphism/tensor.hpp>
#include <iostream>

namespace iso = isomorphism;
using namespace iso::math;

int main() {
    // Create a batch of 4 random 3×3 matrices
    iso::Tensor A = random_normal({4, 3, 3}, iso::DType::Float32);
    iso::Tensor I = eye(3, iso::DType::Float32);

    // Batched matmul — shape stays {4, 3, 3}
    iso::Tensor result = matmul(A, broadcast_to(I, {4, 3, 3}));

    // Pull a scalar to CPU
    std::cout << "trace[0] = " << to_double(slice(trace(result), 0, 1, 0)) << "\n";
    return 0;
}
```

The API covers element-wise arithmetic, batched linear algebra (`solve`, `svd`, `qr`, `inv`, `det`, `matrix_exp`), reductions, random sampling and FFTs. Native MLX arrays, torch tensors and Eigen matrices can be wrapped and unwrapped without copying.
