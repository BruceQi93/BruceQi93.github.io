---
layout: post
title: "Unity编辑器扩展 - 寻找脚本引用"
date: 2017-6-3
image: '/assets/img/'
description: '在工程中寻找一个脚本的所有引用.'
tags:
- 编辑器扩展
categories:
- Unity编辑器 
---

> 我们有时候会在一个项目开发中途进入到项目组，这时就需要我们去熟悉了解这个项目，去看该项目的源码。有的项目的工程组织结构不是很清晰，比较乱，这会给我们熟悉项目带来阻碍。例如，某一个脚本我们想知道工程中那些地方对他进行了引用，一个一个物体的找吗？No效率太低了，我们可以用编辑器扩展写一个小工具来快速查找一个脚本在工程中的所有引用。

#### 第一种方法

　　该方法需要选择一个物体和一个脚本，然后点击Find按钮，就可以查找到该物体及其子物体中所有要查找的脚本的引用。  
具体代码如下：
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;

public class MonoFinder : EditorWindow
{
    Transform root = null;
    MonoScript scriptObj = null;
    int loopCount = 0;

    List<Transform> results = new List<Transform>();

    [MenuItem("Finder/FindScript")]
    static void Init()
    {
        EditorWindow.GetWindow(typeof(MonoFinder));
    }

    void OnGUI()
    {
        GUILayout.Label("节点：");
        root = (Transform)EditorGUILayout.ObjectField(root, typeof(Transform), true);
        GUILayout.Label("脚本类型：");
        scriptObj = (MonoScript)EditorGUILayout.ObjectField(scriptObj, typeof(MonoScript), true);
        if (GUILayout.Button("Find"))
        {
            results.Clear();
            loopCount = 0;
            //Debug.Log("开始查找");
            FindScript(root);
        }
        if (results.Count>0)
        {
            foreach (Transform t in results)
            {
                EditorGUILayout.ObjectField(t, typeof(Transform), false);
            }
        }
        else
        {
            GUILayout.Label("无数据");
        }
    }

    void FindScript(Transform root)
    {
        if (root!=null&&scriptObj!=null)
        {
            loopCount++;
            //Debug.Log(".." + loopCount + ":" + root.gameObject.name);
            if (root.GetComponent(scriptObj.GetClass())!=null)
            {
                results.Add(root);
            }
            foreach (Transform t in root)
            {
                FindScript(t);
            }
        }
    }
}
```

#### 第二种方法
　　第二种方法可以获取到选择的文件被哪些预设和场景引用，并不仅仅包括脚本，材质、贴图等文件都可以查找到。  
具体代码如下：
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;
using System.IO;

public class FindScriptReference : MonoBehaviour 
{
	[MenuItem("Assets/Tool/GetReference")]
    static void GetReference()
    {
        string target = "";
        if (Selection.activeObject!=null)
        {
            target = AssetDatabase.GetAssetPath(Selection.activeObject);
        }
        if (string.IsNullOrEmpty(target))
        {
            return;
        }
        string[] files = Directory.GetFiles(Application.dataPath, "*.prefab", SearchOption.AllDirectories);
        string[] scene = Directory.GetFiles(Application.dataPath, "*.unity", SearchOption.AllDirectories);

        List<Object> fileList = new List<Object>();
        for (int i = 0; i < files.Length; i++)
        {
            string[] source = AssetDatabase.GetDependencies(new string[] { files[i].Replace(Application.dataPath, "Assets") });
            for (int j = 0; j < source.Length; j++)
            {
                if (source[j]==target)
                {
                    fileList.Add(AssetDatabase.LoadMainAssetAtPath(files[i].Replace(Application.dataPath, "Assets")));
                }
            }
        }
        for (int i = 0; i < scene.Length; i++)
        {
            string[] source = AssetDatabase.GetDependencies(new string[] { scene[i].Replace(Application.dataPath, "Assets") });
            for (int j = 0; j < source.Length; j++)
            {
                if (source[j]==target)
                {
                    fileList.Add(AssetDatabase.LoadMainAssetAtPath(scene[i].Replace(Application.dataPath, "Assets")));
                }
            }
        }
        Selection.objects = fileList.ToArray();
    }
}
```

---

