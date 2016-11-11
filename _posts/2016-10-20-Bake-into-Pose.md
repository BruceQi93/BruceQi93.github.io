---
layout: post
title: "角色动画的apply root motion和bake into pose问题"
date: 2016-10-20
image: '/assets/img/'
description: '关于模型中的动画的位移变换问题.'
tags:
- Unity3d引擎 
- 动画系统
categories:
- Unity3d引擎
---

## # 问题

导入到unity3d的一些带动画的模型，有的动画会有位移变换，有的则没有。而是否将模型在动画中的位移变换，应用到模型在场景中的位移，有两个地方需要设置：**Apply Root Motion** 和 **Bake into Pose**，因此unity中将动画的变换分成两种：**Root Transform** 和 **Body Transform**。


## # 具体分析

- 1.如果设置Root Transform，将会影响模型的实际位置，前提是需勾上Apply Root Motion，如果不勾上变换将不应用。模型的位置不会改变。
- 2.如果设置Body Transform，在场景中模型位置会发生变换，
但实际的位置将不会发生变化。Apply Root Motion起两个作用：
 - (1).决定是否应用Root Transform，如果应用，在播放动画的同时，模型的位置会同时发生变化，如果不勾选，将不应用Root Transform。所有的变化将不起作用。
 - (2).在动画结束后，将Body Transform中的变换在动画结束后应用到模型。

下面将分为四种情况(×代表不勾选，√代表勾选)：

 情况 | Apply Root Motion | Bake into Pose
---- | ------------- | ------------
1 | × | √ |
2 | √ | √ |
3 | √ | × |
4 | × | × 

*具体说明：*

- 1.不勾选“apply Root motion”，勾选“bake into pose”，属于Body  Transform，在场景中，如果模型动画带位移，会自动播放位移，但根结点不会位移，所以动画结束后，变化不会应用到模型，模型仍在起始位置。
- 2.属于Body Transform，动画过程中模型位置不会变化，动画结束后，变换会应用到模型，模型处于动画结束时的位置。
- 3.属于Root Transform，变化应用到模型，动画过程中模型跟着动画不断地变换，结束后，模型处于动画结束时的位置。
- 4.属于Root Transform，但变换不被应用，所以模型一直在本地不动。


## # 总结

- 1.勾选上Apply Root Motion，模型的实际位置会随动画中模型的位移而发生变化；不勾选就不发生变换。
- 2.勾选上Bake into Pose，播放有位移的动画时，模型实际位置不会改变，直到动画结束，其位置才改变；不勾选，模型随动画中位移而位移。


---

*注：部分资料参考网上，然后个人在Unity工程中进行实验过，总结而成。*