---
layout: post
title: "【Unity热更新1】- 热更初探"
date: 2017-4-21
image: '/assets/img/'
description: '介绍Unity如何进行热更新.'
tags:
- Lua
- 热更新
categories:
- Unity热更新 
---

> 热更新：可以不重新下载客户端，而更新游戏内容。

#### 热更新原理

热更新涉及的3个目录：

　　游戏资源目录：包含Unity工程中StreamingAssets文件夹下的文件。安装游戏之后，这些文件会被复制到目标机器上的特定文件夹里。不同平台的该文件夹目录如下：

Mac OS/Windows:Application.dataPath+"/StreamingAssets";

Ios:Application.dataPath+"/Raw";

Android:"jar:file://"+Application.dataPath+"!/assets";

　　数据目录：“游戏资源目录“在Android和iOS上只读，不能把网上下载的资源放到里面，所以需要建立一个可读可写的“数据目录”，第一次开启游戏后，程序将“游戏资源目录”的内容复制到“数据目录”，这个步骤只执行一次，下次再打开游戏不再复制。游戏过程中的资源加载，都是从“数据目录”中获取、解包。LuaFramework定义的数据目录如下：

Mac OS/Windows:c:/LuaFramework/

Android/Ios:Application.persistentDataPath+"/LuaFramework"

调试模式下：Application.dataPath+"/StreamingAssets/"


　　网络资源地址：存放游戏资源的网址，游戏开启后，程序会从网络资源地址下载一些更新的文件到数据目录。这些目录包含不同版本的资源文件，以及用于版本控制的files.txt(里面存放这资源文件的名称和md5码)。程序先下载“网络资源地址”上的files.txt，然后与“数据目录”中的文件的md5码比较，更新有变化的文件。

#### 如何利用Lua进行热更新

在移动端编写Lua的解析器，通过这个解析器，可以运行最新的Lua脚本，然后C#代码不需要变动，把控制游戏逻辑的代码都写成Lua脚本，以后只需要修改Lua脚本就好了。


---
*注：以上内容来源于网上搜集整理*

