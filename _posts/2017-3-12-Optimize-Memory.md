---
layout: post
title: "项目优化之针对内存"
date: 2017-3-12
image: '/assets/img/'
description: '在项目中对内存进行优化.'
tags:
- 优化
- 内存
categories:
- 优化 
---

> 内存方面的开销主要来源于Unity内部内存占用和Mono托管内存占用。

#### 资源内存占用

1、纹理

* 不透明贴图压缩格式：ETC 4bit(安卓），PVRTC(iOS)；透明贴图压缩格式：RGBA 16bit或RGBA 32bit。
* 尽量降低纹理尺寸。如果512×512的纹理显示效果可以的话，就不要用1024×1024的纹理。
* 开启mipmap功能。目的是降低渲染带宽的负荷，提升游戏的渲染效率。3D场景模型和角色建议开启Mipmap功能，但UI纹理就没必要开启Mipmap功能了。此外，2D游戏也不需要开启Mipmap功能。
* 关闭Read&Write选项。开启该选项会使纹理内存增大一倍。

2、模型

* 尽量控制模型的面数，在300~1500之内比较合适。
* 尽量保持骨骼数在30根之内。
* 材质数量最好不要超过3个。

3、音频

* BGM使用.ogg或.mp3的压缩格式。
* 音效使用.wav或.aif的不压缩格式。


#### Mono托管内存占用

Unity中Mono可以自动地改变堆的大小来适应所需要的内存，并适时地调用GC(垃圾回收)来释放已经不需要的内存。但存在一个问题，Mono的堆内存一旦分配，就不会返还给系统，这意味这Mono的堆内存只升不降，产生了大量的无效堆内存。


#### GC(垃圾回收)

* 字符串连接会产生一个新的字符串，而之前的字符串就成为了垃圾，而作为引用类型的字符串，其空间是在堆上分配的，被弃置的旧字符串的空间会被GC当做垃圾回收。使用string.format或stringBuilder代替。
* 尽量不要使用foreach，而使用for。foreach会涉及到迭代器的使用，而每循环一次迭代器会产生24Bytes垃圾。
* 不直接访问gameobject的tag属性。把go.tag=="human"换成go.CompareTag("human")，因为访问物体的tag属性会在堆上额外分配空间。
* 使用对象池。实现空间的重复利用。
* 尽量使用struct而非class，因为struct是栈区，class是堆区。

---
*注：以上内容来源于网上搜集整理*

