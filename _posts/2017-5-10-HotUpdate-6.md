---
layout: post
title: "【Unity热更新6】- LuaFramework示例解析"
date: 2017-5-10
image: '/assets/img/'
description: '解析LuaFramework中示例的执行流程.'
tags:
- LuaFramework
- 热更新
categories:
- Unity热更新 
---

#### LuaFramework中Main示例场景的执行流程

1、运行游戏，通过执行GameManager物体上的Main.cs脚本，调用该脚本里的Start方法，执行方法里的语句启动游戏。
```cs
void Start()
{
	AppFacade.Instance.StartUp();   //启动游戏
}
```
2、上面的Start方法里调用了APPFacade.cs里的StartUp方法，同时调用了AppFacade构造方法。然后调用AppFacade的InitFramework方法（AppFacade继承了Facade。AppFacade里的构造方法使用了父类即Facade里的构造方法。该构造方法调用了虚方法InitFramework。而子类AppFacade又重写了InitFramework方法）。
```cs
override protected void InitFramework()
{
	base.InitFramework();
	RegisterCommand(NotiConst.START_UP, typeof(StartUpCommand));
}
```
3、AppFacade的InitFramework方法调用了RegisterCommand方法，引向了StartUpCommand脚本，该脚本里初始化所有的管理器（LuaManager、PanelManager、SoundManager、TimerManger、NetworkManager、ResourceManager、ThreadManager、ObjectPoolManager、GameManager），将其加载到GameManager物体上。
```cs    
//-----------------初始化管理器-----------------------
AppFacade.Instance.AddManager<LuaManager>(ManagerName.Lua);
AppFacade.Instance.AddManager<PanelManager>(ManagerName.Panel);
AppFacade.Instance.AddManager<SoundManager>(ManagerName.Sound);
AppFacade.Instance.AddManager<TimerManager>(ManagerName.Timer);
AppFacade.Instance.AddManager<NetworkManager>(ManagerName.Network);
AppFacade.Instance.AddManager<ResourceManager>(ManagerName.Resource);
AppFacade.Instance.AddManager<ThreadManager>(ManagerName.Thread);
AppFacade.Instance.AddManager<ObjectPoolManager>(ManagerName.ObjectPool);
AppFacade.Instance.AddManager<GameManager>(ManagerName.Game);
```
5、GameManager.cs脚本启用，调用CheckExtractResource方法释放资源。判断数据存放目录释放存在，如果存在就启动OnUpdateResource协程，如果AppConst.UpdateMode=true就更新资源下载，否则不更新。如果不存在，就启动OnExtractResource释放协程。等更新完后，就调用OnInitialize方法，调用Game.lua文件中的OnInitOk方法，进入lua逻辑。

4、PanelManager.cs里有一个创建面板的方法CreatePanel(string name, LuaFunction func = null)方法。方法里传入两个参数：面板的名字和一个lua方法。

5、PromptPanel.lua脚本里提供了控件变量（btnOpen、gridParent）给PromptCtrl.lua脚本使用。
```lua
this.btnOpen = transform:FindChild("Open").gameObject;
this.gridParent = transform:FindChild('ScrollView/Grid');
```
6、PromptCtrl.lua里调用了PanelManager里的CreatePanel方法。
```lua
panelMgr:CreatePanel('Prompt', this.OnCreate);
```
7、然后调用lua里的方法OnCreate方法。该方法里得到UIpanelhe LuaBehaviour组件，调用AddClick方法，给btnOpen添加点击事件，调用Loadprefab方法，加载预设体。
```lua
--启动事件--
function PromptCtrl.OnCreate(obj)
	gameObject = obj;
	transform = obj.transform;
	
	panel = transform:GetComponent('UIPanel');
	prompt = transform:GetComponent('LuaBehaviour');
	logWarn("Start lua--->>"..gameObject.name);
	
	prompt:AddClick(PromptPanel.btnOpen, this.OnClick);
	resMgr:LoadPrefab('prompt', { 'PromptItem' }, this.InitPanel);
end
```
8、加载预设体方法里调用lua里的InitPanel方法来初始化面板。
```lua
function PromptCtrl.InitPanel(objs)
	local count = 100; 
	local parent = PromptPanel.gridParent;
	for i = 1, count do
		local go = newObject(objs[0]);
		go.name = 'Item'..tostring(i);
		go.transform:SetParent(parent);
		go.transform.localScale = Vector3.one;
		go.transform.localPosition = Vector3.zero;
		prompt:AddClick(go, this.OnItemClick);

		local label = go.transform:FindChild('Text');
		label:GetComponent('Text').text = tostring(i);
	end
end
```
9、在define.lua文件中持有所有的manager的引用，和一些C#脚本的引用。在别的lua文件中只要require "Common/define"，就可以直接使用这些引用了。
```lua
Util = LuaFramework.Util;
AppConst = LuaFramework.AppConst;
LuaHelper = LuaFramework.LuaHelper;
ByteBuffer = LuaFramework.ByteBuffer;

resMgr = LuaHelper.GetResManager();
panelMgr = LuaHelper.GetPanelManager();
soundMgr = LuaHelper.GetSoundManager();
networkMgr = LuaHelper.GetNetManager();

WWW = UnityEngine.WWW;
GameObject = UnityEngine.GameObject;
```
10、如果需要创建一个panel，只需要添加或修改下列文件：  
* 添加xxxPanel.lua、xxxCtrl.lua文件。
* 修改define.lua、Game.lua、CtrlManager.lua。

---
