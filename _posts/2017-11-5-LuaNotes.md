---
layout: post
title: "Lua编程-小记"
date: 2017-11-5
image: '/assets/img/'
description: '一些Lua的小知识点.'
tags:
- lua
categories:
- Lua篇 
---

#### 1. 将字符串分割成一个一个单元，存在表中
* 代码如下：
```lua
	local s = "sofgs啊等级高5584撒旦法规"
	local tb = {}
	for utfChar in string.gmatch(s, "[%z\1-\127\194-\244][\128-\191]*") do  
	    table.insert(tb, utfChar)  
	end
	
	-- 如果要提取出字符串中的数字
	local s1 = ""
	local s2 = ""
	for k,v in pairs(tb) do
	   local n = tonumber(v)
	   if n then
		  s1 = s1..v
	   else
		  s2 = s2..v
	   end
	end
```

----------

#### 2. 删除table中重复的值
* 代码如下：
```lua
	function unique(t, bArray)  
	    local check = {}  
	    local n = {}  
	    local idx = 1  
	    for k, v in pairs(t) do  
	        if not check[v] then  
	            if bArray then  
	                n[idx] = v  
	                idx = idx + 1  
	            else  
	                n[k] = v  
	            end  
	            check[v] = true  
	        end  
	    end  
	    return n  
	end 
```

----------

#### 3. 判断一个table是否是另一个table的子集或相同
* 代码如下：
```lua
	local A = {33,24,11}
	local B = {24,50,33,12,11}
	local C ={}
	local isExistTable = {}
	for i=1,#A do
	    table.insert(isExistTable,false)
	end
	for k1,v1 in pairs(A) do
	    for k2,v2 in pairs(B) do
	        if v1 == v2 then
	            isExistTable[k1] = true                       
	        end
	    end
	end
	local isAdd = true
	for i,v in ipairs(isExistTable) do
	    if v == false then
	        isAdd = false
	    end
	end
	if isAdd then
	    table.insert(C,B)
	end
```

---

