---
title: Open3D/Open3D-ML's Compilation Error-"Compatibility with CMake < 3.5"
date: 2025-12-07 23:54:18 +0200
tags:
   - Open3D
layout: tag

# hidden: true
---

This Error occurs at the building step [official document](https://www.open3d.org/docs/release/compilation.html#build) with command:
```
make -j$(nproc)
```

for debug use, build with single cpu
```
make -j1 VERBOSE=4
```


Error message: 
```
CMake Error at build/<3rd-party-libs> (cmake_minimum_required):
  Compatibility with CMake < 3.5 has been removed from CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.

  Or, add -DCMAKE_POLICY_VERSION_MINIMUM=3.5 to try configuring anyway.
```

While configure with `-DCMAKE_POLICY_VERSION_MINIMUM=3.5` do not work!

Instead of fixing the `CMakeLists.txt` for each build library (Ex: change `VERSION 2.8.12` to `VERSION 3.5`), 
```
# in CMakeLists.txt
cmake_minimum_required(VERSION 2.8.12)
```

It's better to set `CMAKE_POLICY_VERSION_MINIMUM=3.5` as an enviroment variable.

```
$ export CMAKE_POLICY_VERSION_MINIMUM=3.5
$ cmake -DBUILD_CUDA_MODULE=ON -DGLIBCXX_USE_CXX11_ABI=OFF -DBUILD_PYTORCH_OPS=ON -DBUILD_TENSORFLOW_OPS=OFF -DBUNDLE_OPEN3D_ML=ON -DOPEN3D_ML_ROOT="https://github.com/isl-org/Open3D-ML.git" -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_VERSION_MINIMUM=3.5 -DBUILD_GUI=ON -DBUILD_WEBRTC=ON -DBUILD_JUPYTER_EXTENSION=ON ..
```

