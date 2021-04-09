---
title:  "交叉编译-openssl"
categories: 交叉编译
tags: arm linux 交叉编译 编译
---

OpenSSL所用版本为openssl-1.1.1j

## 准备工作

下载：[OpenSSL官网下载](https://www.openssl.org/source/) 文档所写时最新版本为：openssl-1.1.1j.tar.gz

```shell
WORK_DIRECTION=~/cross_compile # 工作目录和下载文件存放目录
cd $WORK_DIRECTION
OPENSSL_BUILD_PATH=$WORK_DIRECTION/openssl-build # curl 的构建目录
# 交叉编译前缀
CROSS_COMPILE_PREFIX=/opt/gcc-sigmastar-4.9.4/arm-buildroot-linux-uclibcgnueabihf-4.9.4-uclibc-1.0.31/bin/arm-linux-
unar openssl-1.1.1j.tar.gz # 解压
mkdir -p $OPENSSL_BUILD_PATH && cd $OPENSSL_BUILD_PATH # 独立目录构建
```

**openssl-1.0.0**版本不能在独立的目录构建。

## 配置

**如有时间浏览下根目录下的 INSTALL 文件，其中详细描述各种环境的openssl构建过程。**

安装目录为：`--prefix=/xxx/xxx`指定的目录（绝对路径）

传入平台相关参数：linux-armv4（可运行`./Configure --help`查看有那些已支持的平台）

`./config`可自动检测平台，但这样makefile中会产生-m64的编译选项，所以这里使用`./Configure`直接传入平台参数linux-armv4

```shell
../openssl-1.1.1j/Configure \
--prefix=$OPENSSL_BUILD_PATH/install \
--cross-compile-prefix=$CROSS_COMPILE_PREFIX \
no-asm \
shared \
linux-armv4
```

## 编译安装

```shell
make -j
make install
```
