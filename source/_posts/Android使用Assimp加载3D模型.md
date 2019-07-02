---
title: Android使用Assimp加载3D模型
date: 2019-07-01 19:13:13
tags: [ndk,assimp,cmake]
---

### CMake编译

Android Studio 编译原生库的默认编译工具是 [CMake](https://cmake.org/)。由于很多现有项目都使用 ndk-build 编译工具包，因此 Android Studio 也支持 [ndk-build](https://developer.android.com/ndk/guides/ndk-build.html)。不过，如果您要创建新的原生库，则应使用 CMake。CMake是C或者C++的编译工具，对于C开发应该来说是更友好的，用CMake同时也能使jni开发更加简单，所以项目使用CMake编译的方案。

#### CMakeLists语法

谷歌开发者文档参考在Gradle中配置[Cmake](https://developer.android.com/ndk/guides/cmake)

简单介绍常用的一些语法

首先必须先指定CMake的使用版本 `cmake_minimum_required(VERSION 3.4.1)`

添加我们要编译的c或者c++代码

```cmake
add_library(libName SHARED hello.cpp)
```

add_library可以把要参与编译的源代码编成库，这里libName表示库的名字，SHARED表示.so结尾的库，STATIC表示.a结尾的库。实际使用肯定有很多c文件，不可能一个一个文件添加。使用如下方式添加文件列表

```Cmake
//把目录下所有的cpp文件添加到ROOT_SOURCE文件列表中
file(GLOB ROOT_SOURCE *.cpp)
//编译ROOT_SOURCE列表里所有文件生成so库
add_library(libName SHARED ${ROOT_SOURCE})
```

ndk最主要的目的是能使用现有的一些C的项目，下面说下添加其他预构建库。

详细的使用Android Studio配置NDK项目[请参考谷歌开发文档](https://developer.android.com/studio/projects/add-native-code.html)

添加预构建库与为 CMake 指定要构建的另一个原生库类似。不过，由于库已经预先构建，您需要使用 [`IMPORTED`](https://cmake.org/cmake/help/latest/prop_tgt/IMPORTED.html#prop_tgt:IMPORTED) 标志告知 CMake 您只希望将库导入到项目中：

```cmake
add_library( imported-lib
             SHARED
             IMPORTED )
```

然后，您需要使用 [`set_target_properties()`](https://cmake.org/cmake/help/latest/command/set_target_properties.html) 命令指定库的路径，如下所示。

```cmake
add_library(...)
set_target_properties( # Specifies the target library.
                       imported-lib

                       # Specifies the parameter you want to define.
                       PROPERTIES IMPORTED_LOCATION

                       # Provides the path to the library you want to import.
                       imported-lib/src/${ANDROID_ABI}/libimported-lib.so )
```

为了确保 CMake 可以在编译时定位您的标头文件，您需要使用 `include_directories()` 命令，并包含标头文件的路径:

```cmake
include_directories( imported-lib/include/ )
```

假如我需要添加整个三方项目进入CMake一起编译，比如OpenGL开发中需要用到glm，则可以将整个glm项目添加到项目中一起编译。只需要把路径include进来就可以了。

```cmake
include_directories(glm-0.9.7.5)
```

