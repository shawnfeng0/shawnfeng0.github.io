---
title: "在Manjaro上折腾i3-wm"
categories: 软件工具
tags: 窗口管理
---

最近在尝试i3-wm这款窗口管理器，一是因为我笔记本偶尔比较卡想节省点内存，再是想换个窗口管理器尝试让工作效率更高，更专注，还有就是想折腾。

我已经使用manjaro系统三年多了，一直用KDE桌面，各方面都非常好，就是有一次我不小心按到键盘触发了界面设置，不知道咋回事卡死了，强制重启后就不能联网了，设置也失败，我只能手动用命令联网，应该是network manager和桌面wifi设置应用通信出了问题。正好我准备尝试别的桌面环境，借此机会就开始尝试i3。

## 基本环境

电脑intel nuc，显示是通过两个HDMI连接的屏幕，声音通过其中一个输出，使用wifi联网。

## 安装

i3在manjaor官网的社区里可以找到下载，我现在是manjaro-i3-21.0.7。使用[etcher](https://www.balena.io/etcher/)或者[ventoy](https://github.com/ventoy/Ventoy)把镜像写入到U盘安装。

## 修复

毕竟是社区版本的，安装完成后确实发现一些问题，到处找各种论坛找解决方法。

### 音量无法调节/没有声音

i3的音量控制图标是`volumeicon`，打开配置后显示使用`alsamixer`控制音量, `alsamixer`直接控制硬件设备，但是无法获取到哪个硬件可用。

找了一圈资料了解到现在linux中稳定的音频管理器是`PulseAudio`（也叫声音服务器），已经被安装过了，它可以检测到所有音频输出设备，而且还能控制不同应用程序的音频输出，在KDE中也是用的它。前端界面可以使用`pavucontrol`控制`pulseaudio`，用了一下pavucontrol改了输出，果然有声音了，接下来就是怎么把alsamixer和pulseaudio结合起来。

我在尝试i3之前还尝试了社区已经不再维护的[bspwm20.0.1](https://osdn.net/projects/manjaro-archive/storage/bspwm/20.0.1/)，它是可以检测到我的HDMI可以输出音频的，安装完进去找了一下，`bspwm`桌面的`volumeicon`图标打开配置后显示的设备是`pulseaudio`，也就是说`pulseaudio`虚拟了一个声音设备被控制了。开始找资料怎么把`alsa`和`pulseaudio`结合起来，期间看到了[archlinux关于音频的文档](https://wiki.archlinux.org/title/Advanced_Linux_Sound_Architecture_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，了解到alsa的配置文件，看到在bspwm版本的系统中有`/etc/alsa/conf.d/99-pulseaudio-default.conf`配置文件，名字可疑，一看果然是默认使用pulseaudio输出的配置，然后使用`pacman -Qo /etc/alsa/conf.d/99-pulseaudio-default.conf `查了一下这命令是在`pulseaudio-alsa`这个库里面的。于是去i3-wm版本里面安装，之后果然可以控制声音了，解决。

### 桌面上的conky软件无法显示中文

找了一圈资料让把conky的主题文字换成anti，我不知道我系统内有没有anti这个自体，我只知道我有wqy-zenhei，用`pacman -Ql wqy-zenhei`查到它的字体配置文件，进去找到字体的准确名称`WenQuanYi Zen Hei`，替换conky的主题里的自体配置，解决。

## 使用

以前使用KDE，上面的Dolphin文件管理器，ark压缩文件管理器都非常好用。目前打算在i3上面安装继续用KDE的生态，需要的时候再考虑替换。

i3本身的使用网络上有太多的文章，这里不再描述。其他方面等用用再更新。

