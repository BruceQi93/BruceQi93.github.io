---
layout: post
title: "读取Sqlite数据库文件"
date: 2016-12-1
image: '/assets/img/'
description: '如何在Unity中读取Sqlite数据库文件中的数据.'
tags:
- 数据存储与读取
categories:
- C#
---

> Sqlite是一款轻量型的数据库，它占用资源非常的低。在Unity里可以很方便地对Sqlite数据库中的数据进行增，删，改，查等操作。

#### 基本语法
1、增

* insert into 表名 values(值1，值2)
* insert into 表名(列1，列2...)values(值1，值2)

2、删

* delete form 表名 where 列名=值

3、改

* update 表名 set 列名=新值 where 列名=某值

4、查

* select 列名 from 表名

#### 示例
1、在Assets文件下创建Plugins和StreamingAssets文件夹，导入Mono.Data.Sqlite.dll和Sqlite3.dll两个库文件到Plugins文件夹，将创建的数据库文件放到StreamingAssets文件夹。创建的数据库文件如下图：

![img](http://bruceqi93.github.io/assets/img/SqliteTest01.png)

2、创建一个空物体，在其上添加一个脚本，命名为SqliteScript.cs，代码如下：

```s
using UnityEngine;
using System.Collections;
using Mono.Data.Sqlite;//引入命名空间

public class SqliteScript : MonoBehaviour {

    //声明数据库连接对象，通过该对象与数据库文件所在路径进行连接，进而打开数据库
    SqliteConnection con;
    //数据库文件所在路径
    string path;
    //数据库命令
    SqliteCommand command;
    SqliteDataReader reader;
	
	void Start () {
        //要连接的数据库文件路径
        path = "Data Source="+ Application.streamingAssetsPath + "/part01.sqlite";
        //通过路径创建出连接对象
        con = new SqliteConnection(path);
        //打开数据库文件
        con.Open();
        //创建指令对象实例
        command = con.CreateCommand();
        ReadSqlite();
	}
	
	void ReadSqlite()
    {
        #region 第一种执行方式，用于增，删，改操作
        //数据库语句
        command.CommandText = "insert into hero values('张三',10,20,1)";
        //返回受影响的行数
        int count = command.ExecuteNonQuery();
        #endregion

        #region 第二种执行方式，用在查询结果只有一个的情况
        command.CommandText = "select ap,ad from hero where heroName='张三'";
        object obj = command.ExecuteScalar();
        #endregion

        #region 第三种执行方式，返回所有的查询结果
        command.CommandText = "select * from hero";
        reader = command.ExecuteReader();
        //如果读取了下一行，返回值为TRUE，否则为FALSE
        while (reader.Read())
        {
            //把每一行的毎一列读取出来
            for (int i = 0; i < reader.FieldCount; i++)
            {
                print(reader.GetValue(i).ToString() + " ");
            }
        }
        #endregion
    }
}
```

输出结果如下图：

![img](http://bruceqi93.github.io/assets/img/SqliteTest02.png)

---
[工程文件下载地址](https://github.com/BruceQi93/Unity_SqliteTest)

