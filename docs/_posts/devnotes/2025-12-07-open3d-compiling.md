---
title: Open3D/Open3D-ML's configuration Error-"Failed to clone repository"
date: 2025-12-07 20:15:59 +0200
tags:
   - Open3D
layout: tag

# hidden: true
---

This Error occurs when compiling Open3D with Open3D-ML according the [official document](https://www.open3d.org/docs/release/compilation.html#ml-module) with command:
```
# In the build directory
cmake -DBUILD_CUDA_MODULE=ON \
      -DGLIBCXX_USE_CXX11_ABI=OFF \
      -DBUILD_PYTORCH_OPS=ON \
      -DBUILD_TENSORFLOW_OPS=ON \
      -DBUNDLE_OPEN3D_ML=ON \
      -DOPEN3D_ML_ROOT=https://github.com/isl-org/Open3D-ML.git \
      ..
```

Error message: 
**Failed to clone repository: 'https:/github.com/isl-org/Open3D-ML.git'**

This is due to cmake `find_path` function locates at `Open3D/cpp/pybind/CMakeLists.txt`, [code](https://github.com/isl-org/Open3D/blob/82c5ba4e71c33fec8d0c187223c1fb537f5dfbbc/cpp/pybind/CMakeLists.txt#L185) as follows:
```
if (BUNDLE_OPEN3D_ML)
    find_path(
        OPEN3D_ML_ROOT
        NAMES set_open3d_ml_root.sh
        HINTS $ENV{OPEN3D_ML_ROOT}
        DOC "Path to the Open3D-ML repo. This should be set if BUNDLE_OPEN3D_ML is enabled. Alternatively set an env var with the same name to populate this var."
        REQUIRED
        NO_DEFAULT_PATH
    )

    ExternalProject_Add(
        open3d_ml
        PREFIX "${CMAKE_BINARY_DIR}/open3d_ml"
        GIT_REPOSITORY "${OPEN3D_ML_ROOT}"
        GIT_TAG origin/main
        GIT_SHALLOW
        BUILD_IN_SOURCE ON
        # do not configure
        CONFIGURE_COMMAND ""
        # do not build
        BUILD_COMMAND ""
        # do not install
        INSTALL_COMMAND ""
        )
    list(APPEND GENERATED_OUTPUTS open3d_ml)
endif()
```
CMake [find_path](https://cmake.org/cmake/help/latest/command/find_path.html) performs **path normalization** that compress `http://` into `http:/`, resulting in corrupted git url in `build/open3d_ml/tmp/open3d_ml-gitclone.cmake`.
```
$ less build/open3d_ml/tmp/open3d_ml-gitclone.cmake
if(error_code)
  message(FATAL_ERROR "Failed to clone repository: 'https:/github.com/isl-org/Open3D-ML.git'")
endif()
```
even that the CMake doc says that "find_path checks if variable <VAR> is defined. ... If the variable contains any other value the search is not performed.", yet the path normalization still affect the input url.


# How to fix (two solutions)
## Option 1: use local Open3D-ML repo

```
# Pick whereever path for the Open3D-ML repo
$ git clone --depth 1 --branch v0.19.0 https://github.com/isl-org/Open3D-ML.git
```
You don't have to execute `source Open3D-ML/set_open3d_ml_root.sh ` in this point because cmake will do that for you later.

Setting the `OPEN3D_ML_ROOT=<PATH_TO_Open3D-ML>` as:
```
cmake -DBUILD_CUDA_MODULE=ON -DGLIBCXX_USE_CXX11_ABI=OFF -DBUILD_PYTORCH_OPS=ON -DBUILD_TENSORFLOW_OPS=OFF -DBUNDLE_OPEN3D_ML=ON -DOPEN3D_ML_ROOT=/home/belay/Documents/github_others/Open3D/build/Open3D-ML -DBUILD_GUI=ON -DBUILD_WEBRTC=ON -DBUILD_JUPYTER_EXTENSION=ON ..
```


## Option 2: use local Open3D-ML repo
Fix the broken code at `Open3D/cpp/pybind/CMakeLists.txt`, add `if(NOT OPEN3D_ML_ROOT)` before `find_path` function, don't forget the `endif()` in the ending of this block.

```
if (BUNDLE_OPEN3D_ML)
    if(NOT OPEN3D_ML_ROOT)
    find_path(
        OPEN3D_ML_ROOT
        NAMES set_open3d_ml_root.sh
        HINTS $ENV{OPEN3D_ML_ROOT}
        DOC "Path to the Open3D-ML repo. This should be set if BUNDLE_OPEN3D_ML is enabled. Alternatively set an env var with the same name to populate this var."
        REQUIRED
        NO_DEFAULT_PATH
    )
    endif()
```

Now you can execute the configuration with `OPEN3D_ML_ROOT` point to the Open3D-ML.git!
```
cmake -DBUILD_CUDA_MODULE=ON -DGLIBCXX_USE_CXX11_ABI=OFF -DBUILD_PYTORCH_OPS=ON -DBUILD_TENSORFLOW_OPS=OFF -DBUNDLE_OPEN3D_ML=ON -DOPEN3D_ML_ROOT=https://github.com/isl-org/Open3D-ML.git -DBUILD_GUI=ON -DBUILD_WEBRTC=ON -DBUILD_JUPYTER_EXTENSION=ON ..
```

