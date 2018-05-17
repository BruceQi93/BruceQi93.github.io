---
layout: post
title: "获取Text文字的渲染长度"
date: 2018-5-10
image: '/assets/img/'
description: 'Unity里获取一段Text文字渲染后的长度.'
tags:
- Text组件
categories:
- UI 
---

> 在项目中遇到一个需求，一大段Text文字如果一页放不下就放下一页。通过计算单个字符长度再截断，效果不太好，因为字符有的胖些有的瘦些，占用的空间大小不一样。这样只能算出一段文字渲染后的长度，然后去填充。

#### 1.核心代码
```cs
	Font myFont = text.font;
	myFont.RequestCharactersInTexture(message,text.fontSize, text.fontStyle);
	CharacterInfo characterInfo = new CharacterInfo();
	
	char[] arr = message.ToCharArray();
	
	foreach (char c in arr)
	{
	    myFont.GetCharacterInfo(c, out characterInfo, text.fontSize);
	
	    totalLength += characterInfo.advance;
	}
```

　　其中`RequestCharactersInTexture`是指定渲染那些字符，`characterInfo`可以获得生成的去重后字符。`myFont.GetCharacterInfo(c, out characterInfo, text.fontSize)`分别获得没个字符的信息，`characterInfo.advance`就可以得到每个字符的渲染长度。

1. 获取文字渲染长度
```cs
public static float GetWidth(Text uiText, string str)
{
    uiText.font.RequestCharactersInTexture(str, uiText.fontSize, uiText.fontStyle);
    CharacterInfo characterInfo;
    float width = 0f;
    for (int i = 0; i < str.Length; i++)
    {
        uiText.font.GetCharacterInfo(str[i], out characterInfo, uiText.fontSize, uiText.fontStyle);
        width += characterInfo.advance;
    }
    return width;
}
```  

2.获取截取后的字符串
```cs
public static string GetSubString(Text uiText,string str,float maxWidth)
    {
        float totalLength = 0f;
        uiText.font.RequestCharactersInTexture(str, uiText.fontSize, uiText.fontStyle);
        CharacterInfo characterInfo;
        char[] charArr = str.ToCharArray();
        int i = 0;
        foreach (char c in charArr)
        {
            uiText.font.GetCharacterInfo(c, out characterInfo, uiText.fontSize);
            float newLength = totalLength + characterInfo.advance;
            if (newLength>maxWidth)
            {
                if (Mathf.Abs(newLength-maxWidth)>Mathf.Abs(maxWidth-totalLength))
                {
                    break;
                }
                else
                {
                    totalLength = newLength;
                    i++;
                    break;
                }
            }
            totalLength += characterInfo.advance;
            i++;
        }
        return str.Substring(i, str.Length-i);
    }
``` 
    
---

