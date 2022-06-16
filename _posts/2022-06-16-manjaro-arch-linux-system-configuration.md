---
title: "Manjaro/ArchLinux系统配置"
categories: android
tags: linux android adb
---

## 使用常规的rc.local开机运行脚本

archlinux使用systemd作为开机启动控制工具，如果想添加开机脚本需要自己写服务配置，rc-local是以前简单的开机启动脚本，在/etc/rc.local里面写脚本就可以开机运行。为了兼容这种旧的开机脚本。archlinux社区有人写了简单的rc-local开机服务，开机可以自动运行rc-local里面的脚本。

安装rc-local：

```shell
yay -S rc-local
systemctl enable rc-local.service
```

接下来在rc-local里面填写需要开机运行的脚本即可。
