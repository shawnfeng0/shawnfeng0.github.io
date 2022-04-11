---
title: "交叉编译opus音频解码库"
categories: 交叉编译
tags: opus
typora-root-url: ..
---

交叉编译opus文件解码器库（`opusfile`）到`android`平台上。

## 准备工作

### opus库

`git clone`下载`opus`库，`ogg`库，`opusfile`库。（使用最新的稳定版本库2022-04-11）

[opus主页](https://github.com/xiph/opus) ：切换到v1.3.5的git标签

[ogg主页](https://github.com/xiph/ogg)：切换到v1.3.1的git标签

[opusfile主页](https://github.com/xiph/opusfile)：`master`分支：`commit cf218fb54929a1f54e30e2cb208a22d08b08c889`

`opusfile`稳定版本无法使用`cmake`编译，`master`分支刚添加了`cmake`编译的支持，所以使用`master`分支。

### ndk库

下载android-ndk，我们目前统一使用这个：

[android-ndk-r21e-linux-x86_64下载](https://github.com/android/ndk/wiki/Unsupported-Downloads#r21e)

### 目录结构

都下载解压完后如下放置：

```shell
$ tree -L 1
.
├── android-ndk-r21e
├── build.sh
├── CMakeLists.txt
├── ogg
├── opus
└── opusfile

4 directories, 2 files
```

其中`build.sh`和`CMakeLists.txt`是后添加的，稍后介绍。

## 配置

为了把`opusfile`依赖的`ogg`库和`opus`库集成到一起，需要写`CMakeLists.txt`。

`CMakeLists.txt`很简单：

```cmake
cmake_minimum_required(VERSION 3.5)

project(opus_project)

add_subdirectory(ogg)
add_subdirectory(opus)
add_subdirectory(opusfile)
```

`build.sh`内容：

`opusfile`会生成两个库分别是：`opusfile`，`opusurl`。我们只需要`opusfile`，根据`opusfile`的`CMakeLists.txt`的代码需要给cmake传递`-DOP_DISABLE_HTTP=ON`参数，还有例子也不进行编译：`-DOP_DISABLE_EXAMPLES=ON`。

```bash
#!/bin/bash

# Set work directory and ndk directory
WORKDIR=$(cd "$(dirname "$0")" || exit; pwd)
export ANDROID_NDK=$WORKDIR/android-ndk-r21e

rm -rf build
mkdir -p build && cd build || exit

#refer to: https://developer.android.com/ndk/guides/cmake?hl=zh-cn
cmake -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
	-DANDROID_ABI="armeabi-v7a" \
	-DANDROID_NDK=$ANDROID_NDK \
	-DANDROID_PLATFORM=android-21 \
	-DCMAKE_INSTALL_PREFIX="$WORKDIR"/install \
	-DOP_DISABLE_HTTP=ON \
	-DOP_DISABLE_EXAMPLES=ON \
	..

make opusfile -j8 && make install
```

还需要在`opusfile`的`CMakeLists.txt`配置里面把`ogg`库和`opus`库的依赖来源改掉，不要从系统中搜索。

```diff
diff --git a/CMakeLists.txt b/CMakeLists.txt
index ee37291..9871206 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -22,8 +22,6 @@ option(OP_DISABLE_DOCS "Do not build API documentation" OFF)

 include(GNUInstallDirs)

-find_package(Ogg REQUIRED)
-find_package(Opus REQUIRED)

 include(CMakePushCheckState)
 include(CheckSymbolExists)
@@ -59,8 +57,8 @@ target_include_directories(opusfile
 )
 target_link_libraries(opusfile
   PUBLIC
-    Ogg::ogg
-    Opus::opus
+    ogg
+    opus
     $<$<BOOL:${OP_HAVE_LIBM}>:m>
 )
 target_compile_options(opusfile
```

执行`build.sh`编译即可在`install`目录下生成`ogg`、`opus`、`opusfile`库。

