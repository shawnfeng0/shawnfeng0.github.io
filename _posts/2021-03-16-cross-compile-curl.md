---
title:  "交叉编译-curl"
categories: 交叉编译
tags: arm linux 交叉编译 编译
---

## 准备工作

下载：[curl下载](https://github.com/curl/curl/releases) 文档所写时最新版本为：curl-7.75.0

```shell
unar curl-7.75.0.tar.bz2 # 解压
mkdir -p curl-build && cd curl-build # 独立目录构建
```

curl工具链配置不能写全路径，若PATH环境变量不包含则需要临时添加：

```shell
export PATH=$PATH:/opt/gcc-sigmastar-4.9.4/arm-buildroot-linux-uclibcgnueabihf-4.9.4-uclibc-1.0.31/bin/
```

## 配置

完整配置：

```shell
LDFLAGS=-R/home/feng/cross_compile/openssl-build/install/lib \
../curl-7.75.0/configure \
--with-ssl \
--host=arm-linux \
--target=arm-linux \
--prefix=/home/feng/cross_compile/curl-build/install # 配置
```

解释如下：

### 链接openssl库

官方的`./configure --help`中建议使用`PKG_CONFIG_PATH`环境变量来指示openssl的库文件位置，但实际使用过程未配置成功。

之后改成`LDFLAGS=-R/xxx/openssl_install_dir/lib`才成功找到openssl库的位置。

在configure中传入参数`--with-ssl`。

### 工具链配置

`--host=`和`--target=`两个配置项的解释参考这里：https://gcc.gnu.org/onlinedocs/gccint/Configure-Terms.html

实际配置中使用`--host=arm-linux --target=arm-linux`传入工具链的前缀，最后会调用的是arm-linux-gcc等

### 配置安装目录

`--prefix=/xxx`

## 编译安装

```shell
make -j
make install
```

编译结果输出在 `install` 目录中。

