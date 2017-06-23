---
layout: post
title: "【Unity热更新5】- LuaFramework"
date: 2017-5-6
image: '/assets/img/'
description: 'ToLua框架LuaFramework.'
tags:
- LuaFramework
- 热更新
categories:
- Unity热更新 
---

#### LuaFramework一些脚本作用：

1、Util：对Mono的功能进行封装，这样不继承Mono的类就能使用Mono的东西了（如transform.Find、GetComponent）和一些其他的工具方法。

2、AppFacade：继承Facade，整套框架的入口。

3、Base：继承MonoBehavior，是一切View和Manager的基类；持有各种Manager的引用；能注册移除View的信息。

4、View：只有一个处理消息OnMessage的方法。

5、AppView：继承View，注册处理View消息。

6、LuaManager：继承Manager，入口类。初始化Lua代码加载路径，引用一个LuaState并封装其功能（读取lua文件、调用方法等）。

7、ResourceManager：加载AssetBundle的相关操作。在PC平台上默认加载的是Assets\StreamingAssets里的东西，移动平台上则是Application.persistentDataPath。

8、TreadManager：解包与下载资源。

9、PanelManager:默认lua创建的panel都要在tag为GuiCamera的物体下，提供创建panel的方法。

#### 加载AB包的四个步骤：

* 打包assetbundle。
* 加载总的清单文件。
* 加载assetbundle的依赖文件。
* 加载assetbundle。

先通过Initialize方法加载总的清单文件，然后通过LoadPrefab方法来加载AB包，此时会把这个AB包的请求放在m_LoadQequests中，然后在OnLoadAsset方法对该AB包的所有请求进行处理，通过GetLoadedAssetBundle方法看内存中是否存在这个AB包，如果有就检查该AB包的依赖包是否也在内存中，如果都在，就把请求的包内资源加载进来，并回调方法。如果出现缺包情况，会通过OnLoadAssetBundle方法加载AB包及其依赖包。

#### 热更新的四个步骤：

1、打包：将资源全部打包到StreamingAssets文件夹。

2、解包：在移动端StreamingAssets文件夹是只读的，热更新需要写入文件，因此Application.persistentDatapath这个可读可写的路径才是数据在移动端的存放路径。同时也为了比较MD5值，需要将StreamingAssets的东西解包到Application.persistentDataPath。

3、更新：files.txt文件记录了所有的资源文件及其MD5值，每次进入游戏时都会从服务器下载最新的files.txt，然后对其遍历比较MD5值，如果值不同或不存在则下载。

4、加载：先加载资源的依赖，再加载资源。

#### 框架的工作流程：

C#：

打包后启动游戏，GameManager进行判断，如果是游戏安装后第一次启动，就进行解包，如果AppConst.UpdateMode为false，就不会检测更新，否则就进行更新操作。然后进入初始化操作，调用Game.lua中的OnInitOK方法，进入lua逻辑。

lua：

然后调用指定控制器的Awake方法、PanelManager的CreatePanel方法，调用C#代码，创建panel，为其添加LuaBehaviour，调用xxxPanel.lua的方法，获取控件引用，进行逻辑处理。

---
*[参考资料](http://blog.csdn.net/lyh916/article/details/45021703)*
