---
title : colmap Unsupported gpu architecture
feed: show
date : 15-04-2021
---

## colmap - Unsupported gpu architecture 'compute_native’

### Problem
Compiling colmap failed with error message `Unsupported gpu architecture 'compute_native’`.


### Solution
Check your GPU's [Compute Capability](https://developer.nvidia.com/cuda-gpus). For example, GeForce RTX 3090 has Compute Capability 8.6, then compile with the number:

`cmake .. -GNinja -DCMAKE_CUDA_ARCHITECTURES=86`


### Environment
- OS: Ubuntu 22.04 
- Driver: 545.29.06 
- CUDA: 12.3
- nvcc: 11.5
- 

### Reference
[Cmake - CUDA_ARCHITECTURES](https://cmake.org/cmake/help/latest/prop_tgt/CUDA_ARCHITECTURES.html)