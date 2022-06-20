---
title: "Manjaro/ArchLinux系统配置"
categories: android
tags: linux android adb
---

## 安装中文输入法

manjaro提供一个非常方便的亚洲输入法安装包：manjaro-asian-input-support-fcitx5，安装时会把输入法依赖的其他库全部安装。
安装后再安装对应的语言包即可，比如中文就是fcitx5-chinese-addons，其他的语言的选项会在安装manjaro-asian-input-support-fcitx5时以可选依赖的形式给出，根据名称自行安装即可。

完整脚本：

```shell
sudo pacman -S manjaro-asian-input-support-fcitx5
sudo pacman -S fcitx5-chinese-addons
```

## 使用常规的rc.local开机运行脚本

archlinux使用systemd作为开机启动控制工具，如果想添加开机脚本需要自己写服务配置，rc-local是以前简单的开机启动脚本，在/etc/rc.local里面写脚本就可以开机运行。为了兼容这种旧的开机脚本。archlinux社区有人写了简单的rc-local开机服务，开机可以自动运行rc-local里面的脚本。

安装rc-local：

```shell
yay -S rc-local
systemctl enable rc-local.service
```

接下来在/etc/rc.local里面添加需要开机运行的脚本即可。
