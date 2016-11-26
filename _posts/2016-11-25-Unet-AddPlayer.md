---
layout: post
title: "如何在Unet中添加多个玩家角色"
date: 2016-11-25
image: '/assets/img/'
description: '在Unet中添加多个PlayerPrefab，不同的客户端Spawn出不同的Player模型.'
tags:
- 网络
- Unet
categories:
- Unity3d引擎
---

> 最近在给项目中添加局域网功能时，发现在Unity的新版网络Unet中的NetworkManager下只能添加一个PlayerPrefab，也就是说在每个客户端只能Spawn出相同的角色模型，这在实际游戏项目中是不可能的，整个游戏只有一个模型会使游戏感到单调，然后查了一些资料，在Unity工程中测试了一下，终于找到了解决办法。

### 解决办法
解决方案就是：重写NetworkBehavior中的**OnServerAddPlayer**方法，自己写一个脚本让他继承NetworkManager，不用引擎给的那个NetworkManager组件。
{% highlight ruby %}
     public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId, NetworkReader extraMessageReader)
     	{
			GameObject player = Instantiate(Resources.Load("Player1")) as GameObject;
        	NetworkServer.AddPlayerForConnection(conn, player, playerControllerId);
		}
{% endhighlight %}

然后将Player添加到各个客户端：
{% highlight ruby %}
	 public override void OnClientConnect(NetworkConnection conn)
    	{
        	NetworkMessage test = new NetworkMessage();
        	test.chosenClass = chosenCharacter;

        	ClientScene.AddPlayer(client.connection, 0, test);
    	}
{% endhighlight %}

需要注意的是：要把所有的Player预制体注册进Spawn Prefab中，不然在server端生成了，但在客户端显示不了。

* 另外，引擎提供了一个NetworkManagerHUD组件用来显示网络连接的UI界面，用来简单的测试server端和client端连接，但是很丑，你可以自己做一个好看点的UI，只需要在代码中设置IP地址和端口号，然后写个方法注册到按钮上即可。

我在Unity里做了个小demo，有两个角色cube和capsule，自己做了一个简单的UI界面，可以选择指定的角色。
![img](/assets/img/Unet/Unet-Addplayer.png)
文末有下载地址代码如下：
{% highlight ruby %}
	using UnityEngine;
	using System.Collections;
	using UnityEngine.UI;
	using UnityEngine.Networking;

	public class MyNetWorkManager : NetworkManager {

	    public Text textIP;
	    private int chosenCharacter = 0;
	
	    private string ipAddress;//IP地址
	
	    //子类发送网络消息
	    public class NetworkMessage : MessageBase
	    {
	        public int chosenClass;
	    }
	
	    //在服务器端添加playerprefab
	    public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId, NetworkReader extraMessageReader)
	    {
	        NetworkMessage message = extraMessageReader.ReadMessage<NetworkMessage>();
	        int selectedClass = message.chosenClass;
	
	        if (selectedClass==0)
	        {
	            GameObject player = Instantiate(Resources.Load("Player")) as GameObject;
	            NetworkServer.AddPlayerForConnection(conn, player, playerControllerId);
	        }
	        if (selectedClass==1)
	        {
	            GameObject player = Instantiate(Resources.Load("Enemy")) as GameObject;
	            NetworkServer.AddPlayerForConnection(conn, player, playerControllerId);
	        }
	    }
	
	    //当客户端连接时，在客户端添加Player
	    public override void OnClientConnect(NetworkConnection conn)
	    {
	        NetworkMessage test = new NetworkMessage();
	        test.chosenClass = chosenCharacter;
	
	        ClientScene.AddPlayer(client.connection, 0, test);
	    }
	
	    public override void OnClientSceneChanged(NetworkConnection conn)
	    {
	        //base.OnClientSceneChanged(conn);
	    }
	
	    //选择角色按钮1
	    public void Btn1()
	    {
	        chosenCharacter = 0;
	    }
	
	    //选择角色按钮2
	    public void Btn2()
	    {
	        chosenCharacter = 1;
	    }
	
	    //创建游戏按钮事件
	    public void StartMyHost()
	    {
	        SetMyPort();
	        NetworkManager.singleton.StartHost();
	    }
	
	    //加入游戏按钮事件
	    public void JoinGame()
	    {
	        SetMyPort();
	        SetMyIpAddress();
	        NetworkManager.singleton.StartClient();
	    }
	
	    //设置端口号
	    private void SetMyPort()
	    {
	        NetworkManager.singleton.networkPort = 7777;
	    }
	
	    //设置IP地址
	    void SetMyIpAddress()
	    {
	        ipAddress = textIP.text;
	        NetworkManager.singleton.networkAddress = ipAddress;
	    }
	}
{% endhighlight %}

[工程文件下载地址](https://github.com/BruceQi93/Unity_UnetTest)

*注：以上的工程是在Unity 5.3.4版本下创建的，其他版本可能略有出入，本文系原创。*
