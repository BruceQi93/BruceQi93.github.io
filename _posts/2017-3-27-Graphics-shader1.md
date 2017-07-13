---
layout: post
title: "计算机图形学 - shader分类"
date: 2017-3-27
image: '/assets/img/'
description: '介绍shader的几种类型.'
tags:
- shader
categories:
- 计算机图形学 
---

> shader就是一段运行在GPU上的程序。它负责将输入的Mesh网格以指定的方式和输入的贴图或颜色等组合作用，然后输出，绘图单元可以依据这个输出来将图像绘制到屏幕上。输入的贴图或颜色等，加上对应的shader，以及对shader的特定的参数设置，将这些内容（shader及输入参数）打包存储在一起，得到的就是一个Material，然后我们可以将材质赋予合适的renderer(渲染器)来进行渲染（输出）。

#### shader按管线分类

1、固定功能渲染管线：这是标准的几何&光照(Transforming&Lighting)管线，功能是固定的，它控制着世界、视、投影变换及固定光照控制和纹理混合。T&L管线可以被渲染状态控制，矩阵，光照和材质参数。功能比较有限。基本所有的显卡都能正常运行。

2、可编程渲染管线：对渲染管线中的顶点运算和像素运算分别进行编程处理，而无须像固定渲染管线那样套用一些固定函数，取代设置参数来控制管线。

#### unity中的3种shader

1、Fixed function shader：固定功能着色器，用于高级shader在老式显卡无法显示时的fallback。

2、Surface shader：表面着色器，unity推崇的shader类型，使用unity预制的光照模型来进行光照运算，只需要一个表面处理函数surf即可，使用CG/HLSL语法。

3、Vertex & Fragment shader：顶点&片段着色器，属于可编程渲染管线，使用CG/HLSL语法。

#### 3种shader的异同点

共同点：  

* 都必须从唯一一个subShader开始。
* Properties参数部分，作用及语法完全相同。
* 具体功能都在SubShader里(Subshader: 子Shader,Shader会自上而下运行第一个硬件能支持的Subshader，主要作用是对不用硬件的支持。)
* 都可以打标签，例如Tags { "RenderType" = "Opaque" } LOD 200 以及Lighting On等。
* 都可以Fallback。
* 都可以处理基本的功能，例如光照漫反射(Diffuse)以及镜面反射(Specular)。但是Vertex and Fragment和Surface都能实现Fixed function实现不了的高级功能，例如基于uv计算的效果等等。
  
不同点：

* Fixed function shader以及Vertex and Fragment Shader在subshader下面还有pass{},但是Surface Shader，由于已经将具体内容打包在光照模型了，不能加pass{},加了会报错。
* Fixed function shader每句代码之后没有分号';' 但是V&F shader以及Surface shader每句代码之后都必须加分号';'。
* 核心结构不同：
Fixed function shader的核心是：
Material{} 以及 SetTexture[_MainTex]{}

Vertex and Fragment Shader的核心是：
```cg
CGPROGRAM
#pragma vertex vert
#pragma fragment frag
#include "UnityCG.cginc"
ENDCG
```

surface shader核心是：
```cg
GPROGRAM
#pragma surface surf Lambert
ENDCG
```

---

