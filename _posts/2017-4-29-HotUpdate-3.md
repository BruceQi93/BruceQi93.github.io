---
layout: post
title: "【Unity热更新3】- ToLua里C#与Lua的交互"
date: 2017-4-29
image: '/assets/img/'
description: 'ToLua里C#和Lua的互相调用.'
tags:
- ToLua
- 热更新
categories:
- Unity热更新 
---

> Tolua是uLua的第三代热更方案。

#### C#调用Lua

1、执行一个Lua脚本里的代码
 
    LuaState lua=new LuaState();
    lua.Start();
    string fullPath=Application.dataPath+"/.../.../";(Lua脚本所在的具体路径）
    lua.AddSearchPath(fullPath);
    lua.DoFile("xxx.lua");

2、调用Lua函数

    LuaFunction func=lua.GetFunction("函数名"); 或者 LuaFunction func=lua[函数名] as LuaFunction;
    func.Call();
    或 func.CallFunc();
    Void CallFunc()
    {
	    func.BeginPCall();
	    func.Push();
	    func.PCall();
	    func.EndPCall();
    }
    func.Dispose();		//析构Lua虚拟机

3、访问Lua的数组

    object[] objs=func.Call((object)array)
    objs[i]

#### Lua调用C#

1、访问C#中的数组

    local t=array:ToTable()
    t[i]

---

