# 1. 16-手机系统版本高但Xcode版本低时部署报错的解决

## 1.1. 问题

iOS 手机系统版本高，Xcode 版本低，使用 Xcode 向该手机部署应用时报错，提示如下：

* Please reconnect the device 

或  

* Please check the connection to the device, and review all errors in the Devices and Simulators window

## 1.2. 原因

Xcode更新时，iPhoneOSDeviceSupport 版本不会全部安装，大部分需要自己去添加手机对应的版本

## 1.3. 解决

在 [GitHub-iPhoneOSDeviceSupport](https://github.com/filsv/iPhoneOSDeviceSupport) 或 [Gitee-iOSDeviceSupport](https://gitee.com/Han0/iOSDeviceSupport) 中找到手机系统对应的版本文件，下载，然后放置到 /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport 目录中，然后重启 Xcode 即可。
