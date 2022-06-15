---
title: "linux运行adb设备无权限问题"
categories: android
tags: linux android adb
---

## 解决linux环境下执行adb提示usb设备无权限访问的问题

```shell
echo 'ACTION=="add", SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ENV{ID_USB_INTERFACES}=="*:ff420?:*", MODE="0666"' | sudo tee /etc/udev/rules.d/99-android.rules
sudo udevadm control --reload-rules
sudo udevadm trigger --verbose --action=add --subsystem-match=usb
```

## 参考

<https://stackoverflow.com/questions/9210152/set-up-device-for-development-no-permissions/45006231#45006231>
