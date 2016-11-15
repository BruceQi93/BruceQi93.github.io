---
layout: post
title: "Unity中Asset Bundle的打包和加载"
date: 2016-11-14
image: '/assets/img/'
description: '利用Asset Bundle对资源进行动态下载和加载.'
tags:
- Asset Bundle 
categories:
- Unity3d引擎
---

> Asset Bundle是Unity引擎提供的一种用于存储资源的文件格式，开发者可以通过Asset Bundle将游戏中所需要的各类资源打包压缩并上传到网络服务器上，另外，在运行时游戏可以从服务器上下载改资源，从而实现资源的动态加载。

下面通过一个工程来讲解Asset Bundle的打包和资源的加载。资源之间没有依赖关系的，Unity官方文档有，比较简单，下面讲资源之间有依赖关系的打包和加载。

### 打包

1、在场景中新建一个Cube，将其做成一个预设体，再创建一个材质,命名为red,分别将预设体cube和材质red的Asset Bundle名称命名为cube，red，在Unity中Asset Bundle的名称默认为小写。

2、新建一个C#脚本，命名为BuildAssetBundleScript.cs。代码如下：
{% highlight ruby %}
    using UnityEngine;
    using System.Collections;
    using UnityEditor;

    public class BuildAssetBundleScript : MonoBehaviour {

      [MenuItem("AssetBundle/Build")]
      static void BuildAssetBundle()
      {
    	BuildPipeline.BuildAssetBundles(Application.dataPath + "/AssetBundle");
      }
    }
{% endhighlight %}

*注：通过 BuildPipeline.BuildAssetBundles 方法即可将设置了Asset Bundle的资源全部打包，括号里面是存放Asset Bundle文件的文件夹路径，必须要先在工程中创建出这个文件夹，不然打包的时候会报错。*

### 加载

3、在场景中新建一个空物体，在其上添加一个脚本，命名为LoadAsset.cs。代码如下：
{% highlight ruby %}
	using UnityEngine;
	using System.Collections;

    public class LoadAsset : MonoBehaviour {

      void Start()
      {
         StartCoroutine(Load());
      }

      IEnumerator Load()
      {
        //打包后的资源所在的文件夹
        string assetPath = "file://" + Application.dataPath + "/AssetBundle/";
        //要加载的目标资源的名称
        string realAssetBundleName = "cube";
        //加载总的AssetBundle清单文件
        WWW wwwManifest = WWW.LoadFromCacheOrDownload(assetPath + "AssetBundle", 0);
        //等待资源下载完成
        yield return wwwManifest;
        if (wwwManifest.error == null)
        {
            //得到AssetBundle的总清单
            AssetBundle manifestBundle = wwwManifest.assetBundle;
            //通过清单得到Manifest文件，里面是各个资源之间的依赖关系
            AssetBundleManifest manifest = (AssetBundleManifest)manifestBundle.LoadAsset("AssetBundleManifest");
            //卸载
            manifestBundle.Unload(false);
            //得到目标资源的依赖关系列表，是依赖关系资源的名字
            string[] dps = manifest.GetAllDependencies(realAssetBundleName);
            //保存所有依赖资源
            AssetBundle[] abs = new AssetBundle[dps.Length];
            for (int i = 0; i < dps.Length; i++)
            {
                //各个依赖资源所在路径
                string dUrl = assetPath + dps[i];
                //根据路径下载资源
                WWW dwww = WWW.LoadFromCacheOrDownload(dUrl, 0);
                //等待下载完成
                yield return dwww;
                abs[i] = dwww.assetBundle;
            }
            //加载目标资源文件
            WWW wwwCube = WWW.LoadFromCacheOrDownload(assetPath + realAssetBundleName, 0);
            //等待下载
            yield return wwwCube;
            if (wwwCube.error == null)
            {
                //得到cube资源列表
                AssetBundle cubeBundle = wwwCube.assetBundle;
                //通过资源列表下载资源
                GameObject cube = cubeBundle.LoadAsset(realAssetBundleName) as GameObject;
                Instantiate(cube);
                //卸载资源
                cubeBundle.Unload(false);
            }
          }
        }
      }

{% endhighlight %}

---
[工程文件下载地址](https://github.com/BruceQi93/Unity_AssetBundleTest)

*注：以上的工程是在Unity 5.3.4版本下创建的，其他版本可能略有出入，本文系原创。*