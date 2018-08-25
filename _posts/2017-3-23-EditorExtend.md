---
layout: post
title: "Unity编辑器扩展 - 自定义窗口"
date: 2017-3-23
image: '/assets/img/'
description: '使用编辑器扩展开发小工具.'
tags:
- 编辑器扩展
categories:
- Unity编辑器 
---

#### 自定义编辑器窗口

1、新建一个脚本，引入编辑器命名空间：using UnityEditor;

2、继承EditorWindow再设置MenuItem。
* 代码如下：
```csharp
Public Class MyEditor:EditorWindow
{
	[MenuItem("Window/MyEditor")]
	Static void SetWindow()
	{
		EditorWindow.GetWindow<MyEditor>();
	}
}
```

这样在菜单栏中点击Window->MyEditor就创建出一个自定义的窗口。

3、对创建的窗口进行编辑。
* 代码如下：
```csharp
	private String text;
	Void OnGUI()
	{
		//输入框
		text=EditorGUILayout.TextField("输入文字：",text);
	
		//如果点击了XX按钮
		if(GUILayout.Button("XX"))
		{
			....//按钮点击处理
		}
	
			//提示信息
			EditorGUILayout.LabelField("注意：.....");
	
			//如果窗口的一行有许多控件。会有开始，结束标志，然后在中间定义各种控件。
			EditorGUILayout.BeginHorizontal();//定义行控件开始标志。
			.....//定义控件
			EditorGUILayout.EndHorizontal();//定义行控件结束标志。
	
			//如果再定义第二行控件的话。
			EditorGUILayout.BeginVertical();
			EditorGUILayout.BeginHorizontal();
			....//定义第二行控件
			EditorGUILayout.EndHorizontal();
			EditorGUILayout.EndVertical();
			
			//如果创建了很多行的控件，需要鼠标滚动的话，就在定义这些控件的代码前后分别加上
			EditorGUILayout.BeginScrollView();
			EditorGUILayout.EndScrollView();
	}
	
	//当窗口获得焦点时调用一次
	Void OnFocus()
	{
		......
	}
	
	//当窗口失去焦点时调用一次
	Void OnLostFocus()
	{
		......
	}
```
---

