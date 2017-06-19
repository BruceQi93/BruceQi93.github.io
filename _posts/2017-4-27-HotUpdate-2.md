---
layout: post
title: "【Unity热更新2】- ToLua使用基础"
date: 2017-4-27
image: '/assets/img/'
description: '介绍ToLua热更使用基础.'
tags:
- ToLua
- 热更新
categories:
- Unity热更新 
---

> Tolua是uLua的第三代热更方案。

#### 安装ToLua插件版

Tolua插件版只有最基本的Lua解析，warp，打包等功能，适合大项目集成。

[ToLua下载地址](https://github.com/topameng/tolua)

下载完之后把解压的tolua-master里面的Editor，plugins，Source和Tolua到工程的Assets/ThirdParty/Tolua（具体路径随意）。

#### 修改路径

修改LuaConst中的路径：

public static string luaDir=Application.dataPath+"/Lua";

public static string toluaDir=Application.dataPath+"/ThirdParty/ToLua/ToLua/Lua";

修改CustomSettings中的路径：

public static string saveDir=Application.dataPath+"/ThirdParty/ToLua/Source/Generate/";

public static string luaDir=Application.dataPath+"/Lua/";

public static string toluaBaseType=Application.dataPath+"/ThirdParty/ToLua/ToLua.BaseType/";

#### wrap文件

对于Lua中调用的C#里定义的类，需要将其wrap，才能在lua中正确地调用。

首先在CustomSettings.cs脚本中将C#里定义供lua调用的类添加进customTypeList方法里面。

_GT(typeof(你所定义的类名)),

然后执行“Lua”——>“Gen Lua Wrap Files”。wrap出的文件就会生成在“.../ToLua/Source/Generate"的目录下。

---
*注：以上内容来源于网上搜集整理*

