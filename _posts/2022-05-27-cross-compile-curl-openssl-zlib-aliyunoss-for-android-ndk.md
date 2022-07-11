---
title:  "交叉编译curl/zlib/openssl/aliyunoss"
categories: 交叉编译
tags: ndk
---

## 背景

最近有个跑在android上的应用需要静态链接curl/openssl/oss(阿里云存储)等库，需要交叉编译这些库生成静态库。

几个库的依赖关系：

`aliyun-oss-cpp-sdk` 依赖 `curl`

`curl`依赖 `openssl`和 `zlib`

## 准备工作

编译环境：windows下的wsl-ubuntu子系统

分别下载这几个库，切换到当前最新最稳定的的git分支或标签。
curl: <https://github.com/curl/curl.git> 切换到curl-7_83_1标签

openssl: <https://github.com/openssl/openssl.git> 切换到OpenSSL_1_1_1-stable分支

zlib: <https://github.com/madler/zlib.git> master分支

aliyun-oss-cpp-sdk: <https://github.com/aliyun/aliyun-oss-cpp-sdk.git> master分支

下载ndk21: [android-ndk-r21e-linux-x86_64.zip](https://dl.google.com/android/repository/android-ndk-r21e-linux-x86_64.zip) 自行解压缩

目录结构如下：

```shell
$ tree -L 1
.
├── 1-generate_toolchain.sh
├── 2-openssl_compile.sh
├── 2-zlib_compile.sh
├── 3-curl_compile.sh
├── 4-oss_compile.sh
├── 5-package.sh
├── aliyun-oss-cpp-sdk
├── android-ndk-r21e
├── curl
├── openssl
└── zlib

5 directories, 6 files
```

为了方便调整编译做了几个脚本，脚本内容见下文。

## 生成独立的ndk工具链

1-generate_toolchain.sh脚本：

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")

INSTALL_DIR=$BASE_DIR/android-ndk-r21e-toolchain
"$BASE_DIR"/android-ndk-r21e/build/tools/make-standalone-toolchain.sh --arch=arm --platform=android-21 --install-dir="$INSTALL_DIR"
```

内容解释：调用ndk中生成独立工具链的脚本生成独立工具链。为之后的编译其他库做准备。

## 编译openssl库

2-openssl_compile.sh脚本：

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")
WORK_DIR=$BASE_DIR/build/
INSTALL_DIR=$WORK_DIR/install

BUILD_DIR=$WORK_DIR/openssl-build
mkdir -p "$BUILD_DIR" && cd "$BUILD_DIR" || exit

export ANDROID_NDK=$BASE_DIR/android-ndk-r21e-toolchain
export PATH=$ANDROID_NDK/bin:$PATH

../../openssl/Configure \
    --prefix="$INSTALL_DIR" \
    no-asm \
    no-shared \
    android-arm &&
    make -j &&
    make -j install
```

* 把工具链的bin路径添加到系统PATH路径中

* 设置安装路径

* 执行openssl的config和编译

## 编译zlib库

2-zlib_compile.sh:

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")
WORK_DIR=$BASE_DIR/build/
INSTALL_DIR=$WORK_DIR/install

BUILD_DIR=$WORK_DIR/zlib-build
mkdir -p "$BUILD_DIR" && cd "$BUILD_DIR" || exit

export ANDROID_NDK_TOOLCHAIN=$BASE_DIR/android-ndk-r21e-toolchain
export PATH=$ANDROID_NDK_TOOLCHAIN/bin:$PATH
export CC=arm-linux-androideabi-gcc

../../zlib/configure \
    --prefix="$INSTALL_DIR" \
    --static &&
    make -j &&
    make -j install
```

## 编译curl库

3-curl_compile.sh:

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")
WORK_DIR=$BASE_DIR/build/
INSTALL_DIR=$WORK_DIR/install

BUILD_DIR=$WORK_DIR/curl-build
mkdir -p "$BUILD_DIR" && cd "$BUILD_DIR" || exit

export ANDROID_NDK_TOOLCHAIN=$BASE_DIR/android-ndk-r21e-toolchain
export PATH=$ANDROID_NDK_TOOLCHAIN/bin:$PATH

OPENSSL_INSTALL_PATH=$INSTALL_DIR
ZLIB_INSTALL_PATH=$INSTALL_DIR

../../curl/configure \
    --with-openssl="$OPENSSL_INSTALL_PATH" \
    --with-zlib="$ZLIB_INSTALL_PATH" \
    --host=arm-linux-androideabi \
    --target=arm-linux-androideabi \
    --disable-shared \
    --prefix="$INSTALL_DIR" &&
    make -j &&
    make -j install
```

## 编译oss库

4-oss_compile.sh

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")
WORK_DIR=$BASE_DIR/build/
INSTALL_DIR=$WORK_DIR/install

BUILD_DIR=$WORK_DIR/oss-build
mkdir -p "$BUILD_DIR" && cd "$BUILD_DIR" || exit

ANDROID_NDK=$BASE_DIR/android-ndk-r21e

cmake -DCMAKE_TOOLCHAIN_FILE="$ANDROID_NDK"/build/cmake/android.toolchain.cmake \
    -DANDROID_NDK="$ANDROID_NDK" \
    -DANDROID_ABI=armeabi-v7a \
    -DANDROID_TOOLCHAIN=clang \
    -DANDROID_PLATFORM=android-21 \
    -DANDROID_STL=c++_shared \
    -DCURL_LIBRARY="$INSTALL_DIR"/lib/libcurl.a \
    -DOPENSSL_CRYPTO_LIBRARY="$INSTALL_DIR"/lib/libcrypto.a \
    -DOPENSSL_SSL_LIBRARY="$INSTALL_DIR"/lib/libssl.a \
    -DOPENSSL_INCLUDE_DIR="$INSTALL_DIR"/include \
    -DCURL_INCLUDE_DIR="$INSTALL_DIR"/include \
    -DCMAKE_INSTALL_PREFIX="$INSTALL_DIR" \
    ../../aliyun-oss-cpp-sdk &&
    make -j &&
    make -j install
```

## 打包编译产物

5-package.sh

```shell
#!/usr/bin/bash

BASE_DIR=$(dirname "$(readlink -f "$0")")
WORK_DIR=$BASE_DIR/build/
INSTALL_DIR=$WORK_DIR/install

cd "$INSTALL_DIR" || exit

tar czf curl_openssl_oss_library.tar.gz *
```
