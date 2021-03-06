---
layout: post
title: "Lua语法"
date: 2017-4-18
image: '/assets/img/'
description: 'Lua的基本语法.'
tags:
- Lua语法
categories:
- Unity热更新 
---

> Lua是一个以性能著称的轻量级的脚本语言。可以跨平台运行解析，而不需要编译的过程。Lua是一个区分大小写的编程语言。

[Lua语法学习](http://www.runoob.com/lua/lua-tutorial.html)

#### 变量

1、标识符  
Lua中使用标识符定义一个变量，标识符由字母，数字，下划线组成。最好不要使用下划线加大写字母的标识符，因为Lua的保留字也是这样。一般约定，以下划线开头连接一串大写字母的名字（如_VERSION)被保留用于Lua内部全局变量。
  
2、定义变量  
Lua定义变量是没有类型的，根据存储什么数据来决定是什么类型。如num=10。

3、变量类型  
   (1) nil表示空数据，等同于null。  
   (2) boolean布尔类型，Lua把false和nil看作是“假”。数字0与空字符串“ ”为真。  
   (3) string字符串，用“”或‘’来表示，用2个方括号[[]]来表示“一块”字符串。  
   (4) number小数类型，表示双精度类型的实浮点数。  
   (5) table，其实是一个“关联数组”，数组的索引可以是数字或字符串。Lua中的数组索引是从1
   开始的。

4、Lua变量  
有三种类型：全局变量、局部变量、表中的域。

变量默认是全局的。全局变量不需要声明，给一个变量赋值后即创建了这个全局变量，如果想删除一个全局变量，只需要将变量赋值给nil。删除table表里的变量也是一样的。

局部变量的作用域从声明位置开始到所在语句块结束。

#### 运算符
1、算术运算符。+、—、*、/、%、^(没有++，--)。  
2、关系运算符。<、>、<=、>=、==、~=(表！=)。  
3、逻辑运算符。and、or、not分别表示与、或、非。  
4、连接运算符。、、表连接两个字符串。  
5、一元运算符。#，返回字符串或表的长度。

#### if语句
* 代码如下：
```lua
	1、if 条件 then
	   end
	
	2、if 条件 then
	   else
	   end
	
	3、if 条件 then
	   elseif 条件 then
	   else
	   end
```

#### 循环语句
1、while循环
* 代码如下：
```lua
	while 条件 do
	end
```

2、repeat循环
* 代码如下：
```lua
	repeat
	...
	until 条件
```

3、for循环  
* 代码如下：
```lua
	-- 以step递增循环
	for index = start,end，step do
	...
	end

	-- 以step递减循环
	for index = end,start,-step do
	...
	end
```
  *注：break可以终止循环，没有continue语法。*  
#### 函数
```lua
	function 方法名（参数1，参数2）
	...
	end
```
可变参数，Lua函数可以接受可变数目的参数，在函数参数列表中使用三点(...)表示函数有可变的参数。Lua将函数的参数放在一个叫arg的表中。

#### 迭代器
在Lua中迭代器是一种支持指针类型的结构，它可以遍历集合中每一个元素。
* 代码如下：
```lua
	for k,v in pairs(t) do
	...
	end
```

pairs和ipairs区别
  
同：都能遍历集合（表、数组）
  
异：    
- ipairs只遍历值，按照索引升序遍历，索引中断停止遍历，不能返回nil，只能返回数字0，如果遇到nil则退出，它只能遍历到集合中出现的第一个不是整数的key。
- pairs能遍历集合中的所有元素。还可以返回nil。

#### table（表）

&#8194;&#8194;&#8194;&#8194;table是Lua的一种数据结构用来创建不同的数据类型。如数组、字典等。通过table来解决模块(module)、包(package)和对象(Object)。

1、table的创建
* 代码如下：
```lua
	myTable={}
	myTable={12,3,34,55,"abc"}    
	myTable={name="wang",age=18,isMan=false}
```
2、table的赋值
* 代码如下：
```lua
	myTable[3]=34			//当键是一个数字时的赋值方式
	myTable["name"]="wang"	//当键是一个字符串时的赋值方式
	myTable.name="wang"		//当键是一个字符串时的赋值方式
```
3、table的遍历  
(1).如果是只有数字键，并且是连续的。
```lua
	for index=1,table.getn(myTable) do
	...
	end
```
(2).所有的表都可以通过下面的方式遍历
```lua
	for index,value in pairs(myTable) do
	...
	end
```
4、table元素的内存指向
```lua
	mytable1={}
	mytable1[1]="lua"
	mytable1["test"]="修改前"
	    
	--mytable2和mytable1指向同一个table
	mytable2=mytable1
	    
	print(mytable2[1])	--打印结果为：lua
	print(mytable2["test"])		--打印结果为：修改前
	    
	mytable2["test"]="修改后"
	print(mytable1["test"])		--打印结果为：修改后
	
	--释放变量
	mytable2=nil
	--mytable1仍能访问
	print(mytable1["test"])		--打印结果为：修改后
```
当把表A赋值给另一个表B，两个表指向的是同一个表，当对表B中某一索引重新赋值，表A访问该索引的值为修改的值。如果将表B置为nil，表A仍然能访问元素。

#### Lua元表
Lua提供了元表来改变table的行为，每个行为关联了对应的元方法。

处理元表的函数:

* setmetatable(table,metatable):对指定的table设置元表。
* getmetatable(table):返回对象的元表。

__index元方法：

当通过键来访问table时，如果该键没有值，lua就会寻找该table的metatable中的\_\_index键。如果\_\_index包含一个表格，Lua就会在表格中查找相应的键；如果\_\_index包含一个函数，Lua就会调用那个函数，table和键会作为参数传递给函数。\_\_index元方法查看表中元素是否存在，如果不存在，返回nil；如果存在则由\_\_index返回结果。

Lua查找一个表元素的步骤：  
（1）在表中查找，如果找到，返回该元素，找不到则继续。  
（2）判断该表是否有元表，如果没有元表，返回nil，有元表则继续。  
（3）判断元表有没有\_\_index方法，如果\_\_index方法为nil，则返回nil；如果\_\_index方法是一个表，则重复1、2、3；如果\_\_index方法是一个函数，则返回该函数的返回值。

__newindex元方法：

\_\_newindex元方法用来对表更新，\_\_index则用来对表访问。  
当给表的一个缺少的所有赋值，解释器就会查找__newindex元方法；如果存在则调用这个函数而不赋值。

#### Lua面向对象:(回溯查询)

对象由属性和方法组成，Lua中的类可以用table来描述对象的属性，function表示方法，来进行模拟。至于继承可以通过metatable进行模拟。

一个简单的类继承：
* 代码如下：
```lua
	A={}
	--模拟构造体
	function A:new(o)		--这里相当于function A.new(A,o),self==A。这样写是方便后面继承。new可视为构造函数。
	  o=o or {}				--定义或获取外部一个o表，这个才是模拟的所谓“对象”的实体。
	  setmetatable(o,self)	--让A成为o表的元表。这样o表里没有就找A表读取。
	  self.__index=self		--将__index指向自己，以便新对象在访问A的函数和字段时，可被直接重定向。
	  return o
	end
	
	function A:funName()
	  print('A')
	end
	--从这个类派生出一个子类B，使其打印出类名。则先需要创建一个空的类，从基类继承所有的操作。
	B=A:new()		--创建出一个A的对象，直到现在，B还只是A的一个实例
	S=B:new()		--B从A中继承了new，不过这次new在执行时，它的self参数为B，因此，S的元表为B，B中的字段__index的值也是B。S继承自B，而B又继承自A。
	--B重写funName()函数
	function B:funName()
	  print('B')
	end
	--现在调用
	S:funName()
```

实现封装：(使用Lua中的闭包函数)
* 代码如下：
```lua
	--需要一个闭包函数作为类的创建工厂
	function newAccout(initialBalance)
	  local self={balance=initialBalance}
	  local withdraw=function(v)
	self.balance=self.balance-v
	  end
	  local deposit=function(v)
	self.balance=self.balance+v
	  end
	  local getBalance=function()
	return self.balance
	  end
	  return {withdraw=withdraw,deposit=deposit,getBalance=getBalance}
	end
	
	accl=newAccout(100)
	accl.withdraw(40)
	print(accl.getBalance())
```

#### 模块

&#8194;&#8194;&#8194;&#8194;模块类似于一个封装库，可以把一些公用的代码放在一个文件里，以API接口的形式在其他地方调用，有利于代码的重用和降低代码耦合度。Lua的模块是由变量、函数等已知元素组成的table。
* 代码如下：
```lua
	--文件名为module.lua
	
	module={}		--定义一个名为module的模块
	
	module.constant="这是一个常量"	--定义一个常量
	
	function module.func1()
		io.write("公有函数")
	end
	
	local function func2()
		print("私有函数")
	end
	
	function module.func3()
		fun2()
	end
	
	return module
```

&#8194;&#8194;&#8194;&#8194;func2声明为程序块的局部变量，即表示一个私有函数，不能从外部访问模块里的这个私有函数，必须通过模块里的公有函数来调用。

#### require函数

Lua提供了require的函数用来加载模块。要加载一个模块，只需要调用即可。

require("<模块名>") 或 require "<模块名>"

执行require后会返回一个由模块常量或函数组成的table，并且还会定义一个包含该table的全局变量。

require返回的值将被缓存，即使多次调用require，被调用文件也只运行一次。
* 代码如下：
```lua
	--mod.lua包含print("mod2")
	
	local a=require("mod2")		--输出"mod2"
	
	local b=require("mod2")		--不输出，实际为b=a
	
	Dofile是不缓存的版本的require。
	
	Dofile("mod2")		--输出"mod2"
	Dofile("mod2")		--输出"mod2"
	
	loadfile读取文件但不执行
	
	f=loadfile("mod2.lua")
	
	f()		--输出“mod2”
	
	loadstring读取代码字符串
	
	f=loadstring("print('Lua is cool')")
	
	f()		输出“Lua is cool”
```

*参考文章:*

* [Lua面向对象](http://www.cnblogs.com/stephen-liu74/archive/2012/03/28/2421656.html)
* [Lua面向对象实现](http://www.tuicool.com/articles/QVBBRvq)

---
*注：以上内容来源于网上搜集整理*

