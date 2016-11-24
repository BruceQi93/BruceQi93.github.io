---
layout: post
title: "音频管理类"
date: 2016-11-24
image: '/assets/img/'
description: '对Unity中游戏音效统一管理.'
tags:
- Audio
categories:
- Unity3d引擎
---

>在Unity中游戏开发中，整个游戏中有许多地方要添加音效，在每个地方都添加AudioSource组件是非常消耗资源的，所以建一个音频管理类将所有的音频统一进行管理，将非常的方便。

* 首先新建一个空物体，在其上添加一个脚本，命名AudioManager.cs，用来管理所有的音频片段，代码如下：

{% highlight ruby %}
    using UnityEngine;
	using System.Collections;
	using System.Collections.Generic;

	public class AudioManager : MonoBehaviour {

    //音频片段的数组
    public AudioClip[] audioClipArray;
    //音频库
    private static Dictionary<string, AudioClip> audioDic;
    //背景音乐的音频源
    private static AudioSource bgm;
    //挂载此脚本的游戏对象本身
    private static GameObject _Instance;
	
	void Awake () {
        _Instance = this.gameObject;
        audioDic = new Dictionary<string, AudioClip>();
        //加载音频库
        foreach (AudioClip item in audioClipArray)
        {
            audioDic.Add(item.name, item);
        }
        //指定背景音乐的音频源
        bgm = _Instance.AddComponent<AudioSource>();
	}

    /// <summary>
    /// 播放背景音乐
    /// </summary>
    /// <param name="ac">音频剪辑</param>
    public static void PlayBGM(AudioClip ac)
    {
        if (ac)
        {
            bgm.clip = ac;
            bgm.loop = true;
            bgm.volume = 0.1f;
            bgm.Play();
        }
    }
    
    /// <summary>
    /// 播放背景音乐
    /// </summary>
    /// <param name="acName">音频剪辑的名称</param>
    public static void PlayBGM(string acName)
    {
        if (!string.IsNullOrEmpty(acName))
        {
            AudioClip ac = audioDic[acName];
            PlayBGM(ac);
        }
    }
    
    /// <summary>
    /// 播放游戏音效
    /// </summary>
    /// <param name="ac">音频剪辑</param>
    public static void PlayEffectAu(AudioClip ac)
    {
        if (ac)
        {
            AudioSource[] asArray = _Instance.GetComponents<AudioSource>();
            //从1开始循环，因为0是背景音乐
            for (int i = 1; i < asArray.Length; i++)
            {
                if (!asArray[i].isPlaying)
                {
                    asArray[i].clip = ac;
                    asArray[i].loop = false;
                    asArray[i].volume = 0.5f;
                    asArray[i].Play();
                    return;
                }
            }
            AudioSource newAs = _Instance.AddComponent<AudioSource>();
            newAs.clip = ac;
            newAs.loop = false;
            newAs.Play();
        }
    }

    /// <summary>
    /// 播放游戏音效
    /// </summary>
    /// <param name="acName">音频剪辑的名称</param>
    public static void PlayEffectAu(string acName)
    {
        if (!string.IsNullOrEmpty(acName))
        {
            PlayEffectAu(audioDic[acName]);
        }
    }
	}
{% endhighlight %}

* 将所有用到的音频添加到新建的空物体Inspector面板的AudioManager脚本下的音频片段的数组中。
* 在其他的脚本中需要添加背景音乐的地方，添加一下代码：AudioManager.PlayBGM("背景音乐的名称")；
* 在需要添加音效的地方，添加一下代码：AudioManager.PlayEffectAu("音效名称")；即可。