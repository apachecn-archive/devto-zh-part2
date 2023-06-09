# 我的结构崩溃了 Unity3D 编辑器！

> 原文：<https://dev.to/clpsplug/my-struct-crashed-unity3d-editor-1n79>

当你想做小东西的时候，团结是伟大的。但是，当我在做更大的项目时，是不是只有我发现自己在乱糟糟的代码中？？？最近，我有机会参与一个相对较大的 unity 项目。所以我想我尽力把我游戏的逻辑分成不同的类和结构(它们不继承 MonoBehavior。)

## 背景

我的任务是建立一个六边形地图。 [Red Blob Games](https://www.redblobgames.com/) 有一篇关于[实现六边形网格的精彩帖子。我认为在没有任何提示(除了文章本身)的情况下编写代码将是一个有益的练习。)](https://www.redblobgames.com/grids/hexagons/)

我实现的第一件事是立方体坐标中一个点的值对象。这将是一个不可变的值，所以我选择了`struct.`我做了这样的事情(这只是一个 MCVE:)

```
public struct SomeStruct {
    public int Property 
    { 
        get {
            return Property;
        }
        set {
            Property = value;
        }
    }

    public SomeStruct(int p)
    {
        this.Property = p;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试它，我将它连接到我的一个 MonoBehavior 脚本并创建了一个实例。那么

## 哎呀，我刚撞了 Unity3D 编辑器

编辑消失了。我到处找它；然后 macOS 崩溃记者进来了。报道称，Unity SEGFAULTed。

我曾经遇到过一个例子，由于我的代码中的递归，我使 Unity3D 崩溃了，我成功地跟踪了这个有问题的方法，把它导出为 iOS 的调试版本，因为附带的程序有注释，说明哪个方法是哪个方法。

所以我做了同样的事情，令人惊讶的是，**iOS 应用没有崩溃。**

## 解

iOS app 不崩溃，Xcode 直到 app 崩溃才会指出违规的行，所以没用。因为当我运行我的游戏时 Unity 崩溃了，`Debug.Log`永远不会有帮助。即使 JetBrains Rider 中的断点也无济于事，因为 Unity 崩溃的方式。

我最终放弃了，回到了老方法。我放了一堆“打印到文件”的行，打印该行在哪个文件中的位置。我觉得在这一点上有些事情变得很荒谬。

我终于追查到了这个问题；日志正好在我的结构的构造函数中停止。构造函数是罪魁祸首！

我摆弄了一下属性，发现 *[不允许我们访问构造函数](https://stackoverflow.com/questions/2534960/struct-constructor-fields-must-be-fully-assigned-before-control-is-returned-to)* 中每个属性的支持字段(这就是为什么我不能创建`Property` auto 属性。)

以下是工作代码:

```
public struct SomeStruct {
    public int Property { get; set; }

    public SomeStruct(int p): this() // initialize the base class first
    {
        Property = p;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 由此产生 HexMap 库

之后几乎一帆风顺。我设法用我的库填充了精灵的六边形网格。这里有。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ Clpsplug ](https://github.com/Clpsplug) / [六角形 _ 地图](https://github.com/Clpsplug/hexagonal_map)

### 基于 https://www.redblobgames.com/grids/hexagons/的六边形地图和单元数据结构助手

<article class="markdown-body entry-content container-lg" itemprop="text">

# 六角形地图坐标助手

[红色斑点游戏网站](https://www.redblobgames.com/grids/hexagons/) で説明されている立方体座標 QR 座標のC#での実装
这个 C#代码实现了 [Red Blob Games 网站中描述的基于立方体和 QR 的坐标。](https://www.redblobgames.com/grids/hexagons/)

# 使用

## 协调

有 2 个坐标支持-立方体和 QR(轴向。)
那些值对象的构造函数都是私有的，你必须通过特定的方法，分别做`fromCube`和`fromQR`。

支持的坐标使创建同心六边形地图变得容易。通过偏移坐标可以更好地支持以矩形形式展开的地图。

**注意**你的六边形地图是尖顶的还是平顶的完全取决于你，这些坐标在两种情况下都应该工作正常。

**还要注意**不要直接改变坐标值。你不能。如果你想修改这个值并把它保存在其他地方，只需用修改后的值创建新的坐标对象。

### 立方体坐标

> 让我们取一个立方体网格，切出一个对角线平面…

</article>

[View on GitHub](https://github.com/Clpsplug/hexagonal_map)

## 我还没得到什么

谜团是，为什么 iOS 应用没有崩溃？我的代码试图访问不应该访问的地方，结果崩溃了。是因为 Unity 在导出时把它转换成了 C++，而这种限制似乎并不存在吗？