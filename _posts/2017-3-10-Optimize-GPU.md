---
layout: post
title: "项目优化之针对GPU"
date: 2017-3-10
image: '/assets/img/'
description: '在项目中对GPU方面进行优化.'
tags:
- 优化
- GPU
categories:
- 优化 
---

> GPU负责整个渲染流水线，它会对CPU传过来的数据进行一系列的计算，最后输出到屏幕上。因此，从顶点，像素，带宽等方面对GPU进行优化。

#### 顶点优化

顶点计算是指GPU必须对每个网格上的顶点进行的计算操作，顶点计算主要受到两个因素的影响：需要计算的顶点数量，每个顶点需要进行的操作。

1、优化几何体

剔除不必要的复杂网格，对于不在视图中的物体，不需要过多的细节网格。可以创建面数较低的模型来替代高模。

2、LOD（level of detail）技术

根据摄像机距离对象的远近，选择不同精度的模型，目的是降低显卡的负荷，但缺点是占用更多的内存。

3、遮挡剔除（occlusion culling)技术

遮挡剔除是用来消除在其他物体后面看不到的物体，不用再对那些看不到的顶点进行计算，进而提升性能。

4、层消隐

通过给每个层（layer）设置一个可以看见的距离，当摄像机与设置该层的物体的距离超过这个值，该物体将不会再被渲染。
* 代码如下：
```csharp
    private float[] layers;
    layers=new float[32];
    layers[8]=10;
    layers[9]=20;
    Camera.main.layerCullDistance=layers;
```
#### 像素优化

1、减少overdraw

overdraw是指相同的像素被多次渲染，因此要控制绘制顺序。

2、减少实时光照

使用光照贴图Lightmaps。如果使用unity自带的shader，在表现不差的情况下选择Mobile或Unlit目录下的，它们更高效。


#### 带宽优化

带宽是指GPU在其特定的内存上的读写速率。

1、压缩纹理贴图

压缩贴图比未压缩的32位RGBA贴图占用内存带宽少得多，大大提高渲染表现。

2、mipmap(多重纹理格式）

可以为游戏的贴图生成多重纹理贴图，远处显示较小的物体用小的贴图，显示较大的物体用精细的贴图，这样能更加有效的减少传输给GPU的数据。

#### 锁定帧率
在ProjectSetting->Quality中的VSync Count参数会影响FPS，EveryVBlank相当于FPS=60，EverySecondVBlank=30。

如果需要手动调整FPS，先关闭垂直同步，然后在Awake方法里手动设置FPS,
* 代码如下：
```csharp
    Application.targetFrameRate = 45;
```

降低FPS的好处：可以省电，减少手机发热；稳定游戏FPS，减少卡顿的情况。

待机时，调整游戏的FPS为1，节省电量。

---
*注：以上内容来源于网上搜集整理*

