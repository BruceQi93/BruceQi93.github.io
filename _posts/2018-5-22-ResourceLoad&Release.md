---
layout: post
title: "资源的加载与释放"
date: 2018-5-22
image: '/assets/img/'
description: 'Unity里如何加载与释放资源'
tags:
- 资源加载释放
categories:
- 优化
---

### 资源加载

两种动态加载机制：
1. **Resource.Load**，从一个默认打进程序包里加载AssetBundle。
2. **AssetBundle**，需要自己创建，运行时动态加载，可以指定路径和来源。

---
**AssetBundle的加载：**

1. Create创建
    - CreateFromFile()。从文件读取，不会把整个AssetBundle文件都加载到内存，而是类似建立一个文件操作句柄和缓冲区，需要时才实时Load，所以这种加载方式是最节省资源的，但只能用于standalone程序，加载最快。
    - CreateFromMemory(byte[])。从Memory读取，byte[]可以来自文件读取的缓冲。
    - WWW加载。其实WWW的assetBundle就是内部数据读取完后自动创建了一个assetBundle而已。
    
    *Create完以后，等于把硬盘或者网络的一个文件读到内存一个区域，这时候只是个AssetBundle内存镜像数据块，还没有Assets的概念。*

2. Load加载  
&#8194; &#8194; &#8194;&#8194;用AssetBundle.Load()。这时才会从AssetBundle的内存镜像中读取并创建一个Asset对象，创建Asset对象同时也会分配相应内存用于存放（反序列化）。异步读取用AssetBundle.LoadASync()，一次读取多个用AssetBundle.LoadAll()。

---
**AssetBundle的释放：**

1. AssetBundle.Unload(false)。释放AssetBundle文件的内存镜像，不包含Load创建的Asset内存对象。
2. AssetBundle.Unload(true)。释放AssetBundle文件的内存镜像，并销毁所有用Load创建的Asset内存对象。

---
详解：  
&#8194; &#8194; &#8194;&#8194;Instaniate一个prefab，是一个队Assets进行clone（复制）+ 引用的过程，GameObject，transform是clone生成出来的，其他如texture是纯引用的关系（Instaniate多个GameObject不会重复创建引用对象），mesh、material、shader等是引用和复制同时存在的。引用的Assets对象是不会被复制的，只是一个简单的指针指向已经Load的Asset对象。另外，对于Script，clone一个script等于new一个class实例，实例才会完成工作，把它挂到Unity主线程的调用链里去，class实例里的OnUpdate，OnStart函数才会被执行。多个物体挂同一个脚本，其实是多个物体上挂了那个脚本类的多个实例而已。在new class的过程中，数据区是复制的，代码区是共享的，算是一种特殊的复制+引用关系。所以Load出来的Assets其实是个数据源，用于生成新对象或被引用，生成的过程可能是复制（clone）也可能是引用（指针）。当Destroy一个实例时，只是释放那些clone对象，并不会释放引用对象和clone的数据源对象，Destroy并不知道是否还有别的object在引用那些对象。

*注：系统在加载新场景时，所有的内存对象都会被自动销毁，包括用AssetBundle.Load加载的对象和Instaniate克隆的，但不包括AssetBundle文件自身的内存镜像，那个必须用Unload来释放，这种数据缓存是非托管的*

---
### 总结：
1. #### 加载：
    - **AssetBundle.CreateFrom...**：创建一个AssetBundle内存镜像，注意同一个AssetBundle文件在没有Unload之前不能被再次使用。
    - **WWW.AssetBundle**：同上，要先new一个再yield return然后才能使用。
    - **AssetBundle.Load(name)**：从AssetBundle读取一个指定名称的Asset并生成Asset内存对象，如果多次Load同名对象，除第一次外都只会返回已经生成的Asset对象，即多次Load一个Asset并不会生成多个副本（singleton）。
    - **Resource.Load(path&name)**：同上，只是从默认位置加载。
    - **Instaniate（object）**：clone一个object的完整结构，包括其所有component和子物体，浅copy，并不复制所有引用类型。有个特殊用法可以用Instaniate来完整拷贝一个引用类型的Asset，比如Texture等，要拷贝的Texture必须类型设置为Read/Write able。
2. #### 释放：
    - **Destroy**：主要用于销毁克隆对象，也可以用于场景内的静态物体，不会自动释放该对象的所有引用。如果用于销毁从文件加载的Asset对象会销毁相应的资源文件，但是如果销毁的Asset是copy的或用脚本动态生成的，只会销毁内存对象。
    - **AssetBundle.Unload(false)**：释放AssetBundle文件内存镜像。
    - **AssetBundle.Unload(true)**：释放AssetBundle文件内存镜像，同时销毁所有已经Load的Assets内存对象。
    - **Resources.UnloadAsset(object)**：显式的释放已加载的Asset对象，只能卸载磁盘文件加载的Asset对象。
    - **Resources.UnloadUnusedAssets()**：释放所有没有引用的Asset对象。UnusedAssets不但要没有被实际物体引用，也要没有被生命周期内的变量所引用，才可以理解为Unused(引用计数为0)。
    - **GC.Collect()**：强制垃圾回收器立即释放内存。

---
### 几种加载方式的区别：
1. 静态引用，建一个public变量，在Inspector里把prefab拉上去，用的时候Instaniate。
2. Resource.Load，Load以后Instaniate。
3. AssetBundle.Load，Load以后Instaniate。  
   **区别：**前两种方式，引用对象texture是在Instaniate时加载，而AssetBundle.Load会把prefab的全部Assets都加载，Instaniate时只是生产clone。所以，前两种方式除非你提前加载相关引用对象，否则第一次Instaniate时会包含加载引用Assets的操作，导致第一次加载时的延迟。
    
### 示例分析
1. 实例1  
    &#8194; &#8194; &#8194;&#8194;从某个AssetBundle里Load一个prefab克隆：object = Instaniate(AssetBundle.Load("myPrefab"));如果不需要他你用Destroy(object);你以为就释放干净了，但这时候只是释放了clone对象，通过Load加载的所有引用、非引用Asset对象全都还放在内存里。这时候应该在Destroy以后用AssetBundle.Unload(true),彻底释放干净，如果这个AssetBundle是要反复读取不方便Unload，那可以在Destroy以后用Resources.UnloadUnusesAssets()把所有和这个克隆出来的prefab有关的Asset都销毁。
    
2. 实例2
* 代码：
```csharp
//从磁盘读取一个1.unity3d文件到内存并建立一个AssetBundle1对象
AssetBundle assetBundle1 = AssetBundle.CreateFromeFile("1.unity3d");
//从AssetBundle1里读取并创建一个Texture Asset，吧obj1的主贴图指向它
obj1.renderer.material.mainTexture = AssetBundle1.Load("wall") as Texture;
//把obj2的主贴图也指向同一个Texture Asset
obj2.renderer.material.mainTexture = obj.renderer.material.mainTexture;
AssetBundle1.Unload(true);//obj1和obj2都变成黑的了，因为指向的Texture Asset没了
AssetBundle1.Unload(false);//obj1和obj2不变，只是AssetBundle1的内存镜像释放了
Destroy(obj1);//obj1被释放，但并不会释放刚才Load的Texture
Resources.UnloadUnusedAssets();//这时不会有任何内存释放，因为Texture Asset还被obj2使用
Destroy(obj2);//obj2被释放，但也不会释放刚才Load的Texture
Resources.UnloadUnusedAssets();//这时候刚才Load的Texture Asset释放了，因为没有任何引用了。
GC.Collect();//强制立即释放内存
```

如何加载一大堆图片轮流显示又不爆掉：
* 代码：
```csharp
List<string> fileList = new List<string>();
int n = 0;
IEnumerator OnClick()
{
    WWW image = new WWW(fileList[n++]);
    yield return image;
    Texture tex = obj.mainTexture;
    obj.mainTexture = image.texture;
    n=(n>=fileList.Length-1)?0:n;
    Resources.UnloadAsset(tex);
}
```
---

### 内存种类
- 程序代码
- 托管堆（Managed Heap）
- 本机堆（Native Heap）

1. **程序代码**包括引擎，使用的库和你所写的所有游戏代码。在编译后，得到的运行文件将会被加载到设备中执行，并占用一定内存。  
    **优化：**  
    &#8194; &#8194; &#8194;&#8194;减少打包时的引用库。当使用Unity开发时，默认的Mono包含库可以说大部分用不上，在Player Setting里，将"Api Compatibility Level"选为".NET 2.0 Subset"，表示你只会使用到部分的".NET 2.0 Subset"，不需要Unity将全部.NET的Api包含进去。比较好的解决方案是仍然用最强的剥离库，并辅以较小的第三方的类库来完成所需功能。
2. **托管堆**是被Mono使用的一部分内存，用来存放类的实例（比如用new生成的列表，实例中的各种声明的变量等）。“托管”的意思是Mono“应该”自动地改变堆的大小来适应你所需要的内存，并且定时地使用垃圾回收（Garbage Collect）来释放已经不需要的内存。  
    **优化：**  
    &#8194; &#8194; &#8194;&#8194;托管堆中存储的是你在你的代码中申请的内存。每隔一段时间，Mono的垃圾回收机制将检测内存，将没有再被引用的内存释放回收。你要做的就是在尽可能早的时间将不需要的引用去除掉，这样回收机制才能正确地把不需要的内存清理出来。但是需要注意在内存清理时有可能造成游戏的短时间卡顿，因此如果有大量的内存回收工作要进行的话，需要尽量选择合适的时间（比如按暂停键或切换关卡）。
3. **本机堆**是Unity引擎进行申请和操作的地方，比如贴图，音效，关卡数据等。Unity使用了自己的一套内存管理机制来使这块内存具有和托管堆类似的功能。基本理念是，如果在这个关卡里需要某个资源，那么在需要时就加载，之后在没有任何引用时进行卸载。  
    **优化：**  
    &#8194; &#8194; &#8194;&#8194;当加载完成一个Unity的Scene时，Scene中所有的Asset（包括Hierarchy中所有GameObject上以及脚本中赋值的材质，贴图，动画，音频等素材）都会被自动加载。Scene的所有资源都会被预先加载到内存中，这样导致内存占用变多。因此，尽量减少在hierarchy中对资源的直接引用。而是使用Resource.Load的方法，在需要的时候从硬盘中读取资源。使用完之后用Resource.UnloadAsset()和Resources.UnloadUnUsedAssets()尽快将其卸载掉（这两个Unload方法只对Resource.Load到的资源有效，而不能回收任何场景开始时自动加载的资源）。注意static的单例（singleton）在场景切换时不会被销毁，如果这种单例含有大量对资源的引用，那这些资源将得不到释放。另外，Unity在一个场景开始时根据场景构成和引用关系所自动读取的资源，只有在读取一个新的场景或者reset当前场景时，才会得到清理。

**参考资料：**  
- [内存优化](http://www.cnblogs.com/88999660/archive/2013/03/15/2961663.html])

----------
