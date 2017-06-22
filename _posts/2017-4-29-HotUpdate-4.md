---
layout: post
title: "【Unity热更新3】- ToLua的文件结构"
date: 2017-4-29
image: '/assets/img/'
description: 'ToLua的文件结构和一些脚本的作用.'
tags:
- ToLua
- 热更新
categories:
- Unity热更新 
---

#### ToLua插件文件结构

1、BaseType：存放一些基础类型的绑定代码。

2、Core：提供的一些核心功能。包括封装的LuaFunction、LuaTable等等。

3、Editor：Extend文件夹里是扩展一些类的方法。ToLuaExport(真正生成lua绑定的代码)、ToLuaMenu(菜单功能对应的代码)、ToLuaTree(辅助树结构)。

4、Misc：杂项，目前有LuaClient、LuaCoroutine(协程)、LuaLooper(用于tick)、LuaResLoader(用于加载lua文件)。

5、Reflection：反射相关的代码。

6、Source：Generate文件夹里是生成的绑定代码。LuaConst(一些lua路径等配置文件)。

**Tolua里一些脚本的作用：**

1、LuaAttribute.cs：在Tolua生成绑定代码时做一些标识。

2、LuaBaseRef.cs：Lua中对象对应C#中对象的一个基类，主要作用是有一个reference指向lua里面的对象。引用计数判断两个对象是否相等。

3、LuaState.cs：初始化lua路径，加载相应的lua文件，注册前面生成的绑定代码及各种辅助函数。

4、ObjectTranslator.cs：给lua中对C#对象的交互提供了基础，C#中的对象在传给lua时并不是直接把对象暴露给了lua，而是在这个ObjectTranslator里面注册并返回一个索引，并把这个索引包装成一个userdata传递给lua，并且设置元表。在lua通过传进来的对象调用C#的方法是，它会调用ToLua.CheckObject或ToLua.ToObject从ObjectTranslator获取真正的C#对象。

5、LuaFileUtils.cs：通过.lua文件路径和AssetBundle文件路径这两种方式来找.lua文件，并读取返回byte[]。

6、LuaEvent.cs：类似C#中的Event，提供Add和Remove方法。

7、LuaBinder.cs：如果执行的.lua文件需要用到Unity中的类型，则需要用这个类给LuaState进行绑定。

8、LuaLooper.cs：继承MonoBehavior，在Update/LateUpdate/FixedUpdate中执行对应的LuaEvent。

---

