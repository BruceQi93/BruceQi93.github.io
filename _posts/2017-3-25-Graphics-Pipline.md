---
layout: post
title: "计算机图形学 - 渲染管线"
date: 2017-3-25
image: '/assets/img/'
description: 'GPU的工作原理.'
tags:
- 渲染管线
categories:
- 计算机图形学 
---

> 渲染管线是实时渲染的底层实现。简单来说，就是告诉GPU一堆数据（视点、三维物体、光源、照明模型、纹理等），然后GPU通过一系列计算，将图像绘制在屏幕上的一个过程称为渲染管线。管线的主要功能是生成或渲染二维图像、三维物体、光源、着色方程式、纹理等。生成的二维图像能很好的反应三维物体或三维场景。

#### 渲染管线的三个阶段

1、应用程序阶段：Unity3D做好碰撞检测、视锥剪裁、场景图建立、空间八叉树更新等等计算，CPU、内存把计算好的数据（顶点坐标、法向量、纹理坐标、纹理信息）通过数据总线传给GPU做下一步处理。其中顶点坐标、法向量、纹理坐标放在Vertex buffer中在顶点处理阶段使用，纹理信息放在Texture buffer中在光栅化阶段使用。

2、顶点处理阶段：主要工作就是“变换三维顶点坐标”和“光照计算”。负责顶点坐标变换、光照、裁剪、投影以及屏幕映射，该阶段基于GPU进行运算。
顶点处理的转换操作和对应空间：对象空间（世界转换）-->世界空间（视点转换）-->相机空间（投影转换）-->裁剪空间。
转换后的顶点坐标组成线、片元，超出屏幕外的进行裁剪，这一步就是图元装配和三角形处理。顶点处理阶段完成之后输出转换后的顶点坐标和需要绘制的片元。

3、光栅化阶段：计算片元中每个像素对应的颜色值和深度值保存到像素缓冲区中。这里就要用到应用程序阶段计算产生存放在Texture buffer中的纹理信息。
光栅化阶段的步骤：片元、坐标-->背面剔除-->alpha测试-->模板测试-->深度测试-->融合处理-->抖动处理-->逻辑运算-->像素帧缓冲。

---
