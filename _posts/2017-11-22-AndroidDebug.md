---
layout: post
title: "Unity&Android真机调试"
date: 2017-11-22
image: '/assets/img/'
description: 'Unity项目打包到安卓手机上进行真机调试.'
tags:
- 真机调试
categories:
- 交互 
---

> adb是androidSDK的一个工具，位置就在sdk目录下的platform-tools文件夹下。通过adb，不仅可以查看在Unity中自己设定的打印信息，包括系统信息和异常都能获取到。

#### 1.启动adb
1. 确保手机和电脑在一个局域网下。
2. 打开CMD窗口，输入命令：adb tcpip 5555(打开手机adb网络调试功能)，如果正常的话控制台会回显：restarting in TCP mode port: 5555
3. 打开手机查看手机的IP地址，加入手机IP地址为192.168.1.x，输入命令：adb connect 192.168.1.x，如果正常控制台回显：connected to 192.168.1.x:5555
4. 查看是否连接成功。输入命令：adb devices

#### 2.打包项目调试
在unity中，选择File -> Buld Settings -> 选择 Android，勾选 Development Build 和 Script Debugging这两项。点击Build&Run之后会自动编译文件并将APK推送到手机上安装。程序运行后在Mono中打开Run->Attach to process 会发现你手机的选项，选择手机，在脚本里面添加断点进行调试。

#### 3.直接在控制台看日志
当程序在手机上运行后，在控制台中输入：adb logcat -s Unity。即可在控制台中看到输入日志。清除之前logcat命令：adb logcat -c。将Unity的log信息输出到txt中：adb logcat -s Unity -d > xxx.txt

---

