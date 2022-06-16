---
title: "Windows/Manjaro(ArchLinux)双系统找回linux启动项"
categories: archlinux
tags: boot
---


很多Windows/Linux双系统安装教程会提示先安装windows再安装linux，这样在安装linux的时候会自动在硬盘上安装grub引导。但如果windows后来重装或者顺序错了，linux启动项就没了，需要额外的操作找回linux启动项。

方法是在硬盘的头位置安装grub引导，使用如下命令安装：

```shell
sudo grub-install /dev/nvme0n1
```

/dev/nvme0n1是要安装的硬盘设备，双硬盘双系统的情况也可以在两个硬盘都安装grub（碰到过某些bios不认linux的安装硬盘）。

参考：

<https://wiki.archlinux.org/title/GRUB#Installation_2>
