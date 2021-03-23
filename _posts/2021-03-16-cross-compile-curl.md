---
title:  "交叉编译-curl"
categories: 交叉编译
tags: arm linux 交叉编译 编译
---

## 准备工作

下载：[curl下载](https://github.com/curl/curl/releases) 文档所写时最新版本为：curl-7.75.0

```shell
unar curl-7.75.0.tar.bz2 # 解压
CURL_BUILD_PATH=~/cross_compile/curl-build # curl 的构建目录
mkdir -p $CURL_BUILD_PATH && cd $CURL_BUILD_PATH # 独立目录构建
```

curl工具链配置不能写绝对路径，它会在PATH变量中查找对应工具链，所以若PATH环境变量不包含相应则需要临时添加：

```shell
USED_CROSS_COMPILE_PATH=/opt/gcc-sigmastar-4.9.4/arm-buildroot-linux-uclibcgnueabihf-4.9.4-uclibc-1.0.31/bin/
PATH=$PATH:$USED_CROSS_COMPILE_PATH
```

***此处注意!尽量不要把自己的工具链的路径放在原PATH前面: ~~PATH=\$USED_CROSS_COMPILE_PATH:\$PATH~~***

<details>
<summary markdown="span">点击展开原因</summary>

curl编译需要使用本机的`pkg-config`命令在openssl库的`lib/pkgconfig`中确定openssl的动态库和头文件位置。

而有些厂商的工具链中会包含自己的`pkg-config`软件，使用时会出现错误。

比如我们用的sigmastar的一个工具链找到的openssl库的位置是
`/home/bale.chou/uclibc/buildroot-2017.08/output/host/arm-buildroot-linux-uclibcgnueabihf/sysroot/home/feng/cross_compile/openssl-build/install/lib`

而实际的位置是`/home/feng/cross_compile/openssl-build/install/lib`。

以前用hisi(海思)的工具链的时候人家根本没这个工具，这个工具内部根路径使用的是工具链生成时的主机路径，这不扯淡么。

解决方法是把工具链的`pkg-config`删掉，要么把执行路径放到系统路径后面降低命令搜索优先级：`PATH=$PATH:$USED_CROSS_COMPILE_PATH`

</details>

## 配置

完整配置：

```shell
OPENSSL_INSTALL_PATH=~/cross_compile/openssl-build/install # 已构建好的openssl库的路径
../curl-7.75.0/configure \
PKG_CONFIG_PATH=$OPENSSL_INSTALL_PATH/lib/pkgconfig \
--with-ssl \
--host=arm-linux \
--target=arm-linux \
--prefix=$CURL_BUILD_PATH/install
```

<details>
<summary markdown="span">点击展开解释(更细节的配置请查看官方文档)</summary>

### 链接openssl库

官方的`./configure --help`中建议使用`PKG_CONFIG_PATH`环境变量来指示openssl的库文件位置。PKG_CONFIG_PATH要么export导出，要么放在configure命令中设置，不然会找不到PKG_CONFIG_PATH（这里我没理解）。

如果编译时找不到openssl库或者openssl没有pkg-config就需要手动设置openssl库和头文件位置。
`CPPFLAGS="-I/path/to/ssl/include" LDFLAGS="-L/path/to/ssl/lib" ./configure`

并且在`configure`中传入参数`--with-ssl`。

### 工具链配置

`--host=`和`--target=`两个配置项的解释[参考这里](https://gcc.gnu.org/onlinedocs/gccint/Configure-Terms.html)。

实际配置中使用`--host=arm-linux --target=arm-linux`传入工具链的前缀，最后会调用的是arm-linux-gcc等

### 配置安装目录

`--prefix=/xxx`

</details>

## 编译安装

```shell
make -j
make install
```

编译结果输出在 `install` 目录中。
