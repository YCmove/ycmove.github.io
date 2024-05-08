---
title : colmap error parameter packs not expanded with ‘...’
feed: show
date : 15-04-2021
---

### Problem
colmap make failed with with error message `error: parameter packs not expanded with ‘...’`

### Solution
Make sure you have gcc 10 and g++ 10, if not, install them first `sudo apt install gcc-10 g++-10`

<!-- Change the priority
`sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 50`
`sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 40` -->

Compile under build directory `cd colmap/build`
Set the environment variables for CMake:
- CC: the preferred executable for compiling C language files.
- CXX: the preferred executable for compiling CXX language files.
- CUDAHOSTCXX: the preferred executable for compiling host code when compiling CUDA language files.

```
CC=/usr/bin/gcc-10 CXX=/usr/bin/g++-11 CUDAHOSTCXX=/usr/bin/g++-10 cmake .. -GNinja -DCMAKE_CUDA_ARCHITECTURES=86
```

### Environment
- OS: Ubuntu 22.04 
- Driver: 545.29.06 
- CUDA: 12.3
- nvcc: 11.5

### Reference
[CMake 3.29 doc](https://cmake.org/cmake/help/latest/envvar/CUDAHOSTCXX.html)
