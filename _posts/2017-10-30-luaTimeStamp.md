---
layout: post
title: "Lua编程-时间戳转换"
date: 2017-10-30
image: '/assets/img/'
description: '时间戳在Lua中的使用.'
tags:
- lua
categories:
- Lua篇 
---

> 时间戳是种时间表示方式，指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总秒数。在程序中经常用于标识某一刻的时间。

#### 1. 时间戳转换为具体时间

```lua
local t = 141275362
local time = os.date("%Y/%m/%d/%H/%M/%S",t)
print(time)

输出：1974/06/24/11/09/22
```
----------

获得系统当前时间
```lua
local table = os.date(“*t”,os.time()) 
- 年：table.year，
- 月：table.month，
- 日：table.day，
- 时：table.hour，
- 分：table.min，
- 秒：table.sec
```
----------

#### 2. 具体时间转换为时间戳　　 
```lua
os.time() -- 当前时间戳
os.time({year=2012, month=5, day=17, hour=0, min=0, sec=0}) -- 指定时间的时间戳
```
----------

#### 3. 将两个时间戳间隔转化为具体时间
```lua
function timeStampToTime(gapStamp)    
    local initialStamp = {year = 1970, month = 1, day = 1, hour = 8, min = 0, sec = 0}
    local gapTimeTable = os.date("*t",gapStamp)

    local carry,gapTime = false,{}
    local colMax = {60,60,24,os.date("*t",os.time({year = initialStamp.year, month = initialStamp.month + 1, day = 0})).day, 12 ,0}
    gapTimeTable.hour = gapTimeTable.hour - (gapTimeTable.isdst and 1 or 0) + (initialStamp.isdst and 1 or 0)

    for i,v in ipairs({"sec","min","hour","day","month","year"}) do
        gapTime[v] = gapTimeTable[v] - initialStamp[v] + (carry and -1 or 0)
        carry = gapTime[v] < 0
        if carry then
            gapTime[v] = gapTime[v] + colMax[i]
        end
    end

    return gapTime
end

local t = timeStampToTime(141275362)
print(t)

输出：t:{
	["day"] = 23,
	["year"] = 4,
	["month"] = 5,
	["sec"] = 22,
	["min"] = 9,
	["hour"] = 3,
}
```

---

