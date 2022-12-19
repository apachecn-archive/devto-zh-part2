# pygame、gopherspace 和窗口管理器

> 原文：<https://dev.to/thomasthespacefox/pygame-gopherspace-and-window-managers-3ffi>

有些人可能认为 pygame 主要用于游戏和多媒体，但这并不是它的全部用途。有些人可能还认为你不能在 pygame 中使用多个窗口。虽然后者是真的，但我设法解决了这个问题。

# 什么是 gopherspace

首先，我想简单描述一下 gopherspace 到底是什么。

早在网络出现之前的 1991 年，一种新的协议出现在互联网上。地鼠。gopher 不是超文本，也不像 FTP 目录列表那样粗糙，而是介于两者之间。

Gopherspace 实际上只是一个大的菜单系列。没有脚本，没有比普通格式更多的格式。txt 文档，没有 CSS。这些年来，gopher 逐渐变得淫秽。然而，它仍然存在，部分原因是其荒谬简单的协议:

### 第一步:

客户端连接到服务器。

### 第二步:

客户端发送一个“选择器”,即所需文档的路径，如果是搜索查询，则发送一个 tap+query，然后发送一个 CR+LF。:

```
/some/document[CRLF]
/some/query[tab]some search terms[CRLF] 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:

客户端等待服务器发送选定的完整文档/文件，或者，如果发生错误，通常是包含项目类型“3”的错误页面

## 菜单:

一个 gopher 菜单的 synax 可能再简单不过了(字面上):
通常端口是 70。每行的第一个字符定义了项目的类型。
注意“I”类型有一个“null”选择器字段，因为它甚至不应该是可选的。

```
iI'm an information type[tab]null[tab]server[tab]port[CRLF]
1I'm a gopher menu somewhere[tab]/this/is/a/selector/[tab]server[tab]port[CRLF]
0I'm a text document somewhere[tab]/this/is/a/selector/also.txt[tab]server[tab]port[CRLF]
7I'm a text query (or index search) somewhere[tab]/some/search[tab]server[tab]port[CRLF] 
```

Enter fullscreen mode Exit fullscreen mode

出现某种错误时，返回类型“3”。最常见的是不存在的选择器，类似于 404 错误。

还有很多其他类型和事情，但这是最基本的。

# 我是如何绕过 pygame/sdl 的 1 窗口限制的(算是；d)

现在到了有趣的部分。当你只有一个窗口时，你如何运行多个窗口？虚拟窗口管理器。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [托马斯·西佩斯福克斯](https://github.com/ThomasTheSpaceFox) / [唑酮开窗框架](https://github.com/ThomasTheSpaceFox/Strazoloid-windowing-framework)

### Strazoloid 是 pygame 应用程序和游戏的虚拟窗口管理器。对于创建自定义多窗口环境非常有用。例如许多 2D 模拟游戏。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Strazoloid 窗口框架

v1.4.0
一个 Python/pygame 虚拟窗口管理器框架。
版权所有(c)2018-2020 Thomas Leathers and Contributors

需要帮助吗？关于文档[请参见文档目录](https://raw.githubusercontent.com/ThomasTheSpaceFox/Strazoloid-windowing-framework/master/./docs/index.md)

Strazoloid windowing framework 是自由软件:您可以根据自由软件基金会发布的 GNU 通用公共许可证的条款重新发布和/或修改它，可以是许可证的第 3 版，也可以是(由您选择)任何更高版本。

分发 Strazoloid 窗口框架是希望它有用，但没有任何保证；甚至没有对适销性或特定用途适用性的暗示担保。更多细节请参见 GNU 通用公共许可证。

您应该已经收到了 GNU 通用公共许可证的副本以及 Strazoloid 窗口框架。如果没有，请参见[http://www.gnu.org/licenses/](http://www.gnu.org/licenses/)。

除非另有说明，否则所有图像和其他媒体内容均根据知识共享署名-类似共享 4.0 国际许可协议进行许可。要查看…

</article>

[View on GitHub](https://github.com/ThomasTheSpaceFox/Strazoloid-windowing-framework)

事实上，在研究 zoxenpher 之前，我已经写了 strazoloid 的基本结构，但它已经有了很大的改进。

启动 strazoloidwm 非常简单:

```
import strazoloidwm as stz
desk=stz.desktop(800, 600, "This is the window title", pumpcall=event_test, resizable=1)
#init Framework framesc=stz.framescape(desk)
#create new 'framex' object (virtual window) testframe2=stz.framex(200, 100, "test2", pumpcall=event_test)
#add the frame (window) to our framescape instance. framesc.add_frame(testframe2)
#hand over main thread to framescape instance, starting the window manager. framesc.process() 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问什么？嗯:

```
def event_test(frameobj, data=None):
    #note: you should make sure all non if-statement code is behind if statements. see comments on frame/desktop flags below.
    if frameobj.statflg==1:
        print(frameobj.name + " stat 1: init call")
    elif frameobj.statflg==2:
        print(frameobj.name + " stat 2: post-resize call")
    elif frameobj.statflg==3:
        print(frameobj.name + " stat 3: frame terminate call. type:")
        if frameobj.runflg==0:
            print("   run 0: wm quit call")
            print(frameobj.pid)
            print(frameobj.wo)
        if frameobj.runflg==2:#the desktop class should never set runflg=2
            print("   run 2: frame (window) close call")
            print(frameobj.pid)
            print(frameobj.wo)
    elif frameobj.statflg==4:
        print(frameobj.name + " stat 4: clickdown")
    elif frameobj.statflg==5:
        print(frameobj.name + " stat 5: clickup")
    elif frameobj.statflg==6:
        print(frameobj.name + " stat 6: keydown")
    elif frameobj.statflg==7:
        print(frameobj.name + " stat 7: keyup")
    elif frameobj.statflg==8:
        print(frameobj.name + " stat 8: desktop window resize")
    elif frameobj.statflg==9:
        print(frameobj.name + " stat 9: frame shade")
    elif frameobj.statflg==10:
                print(frameobj.name + " stat 10: frame unshade") 
```

Enter fullscreen mode Exit fullscreen mode

所有的 statflg 检查是什么？好吧。这就是 strazoloidwm 如何让程序知道，框架/桌面对象的' pumpcall '被调用的原因。注意，pumpcall 很容易成为一个类实例方法。

你可以在这里找到完整的测试程序。

# 僵尸

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [托马斯·塞斯佩克斯](https://github.com/ThomasTheSpaceFox) / [佐森弗](https://github.com/ThomasTheSpaceFox/Zoxenpher)

### 一个 python/pygame gopherspace 客户端，复古的桌面 GUI 风格。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 僵尸

python & pygame gopher 客户端。
v3.0.0.indev

动力来自:

*   libgop gopher 库 v0.2
*   strazoloid 虚拟窗口管理器框架 1.4.0 版

[![Screenshot of Zoxenpher](../Images/4ae101ad699365fd8bb0457ef867c663.png)T2】](https://raw.githubusercontent.com/ThomasTheSpaceFox/Zoxenpher/master//vgop/screenshots/zoxenpher1.jpg)

版权所有(c) 2018-2020 托马斯皮革和贡献者

Zoxenpher 是自由软件:你可以在自由软件基金会发布的 GNU 通用公共许可证条款下重新发布和/或修改它，可以是许可证的第 3 版，也可以是 T2(由你选择)的任何更新版本。

发布 Zoxenpher 是希望它有用，但没有任何保证；甚至没有对
适销性或特定用途适用性的暗示担保。更多细节见
GNU 通用公共许可证。

你应该已经收到了一份 GNU 通用公共许可证
和 Zoxenpher。如果没有，见[http://www.gnu.org/licenses/](http://www.gnu.org/licenses/)。

除非另有说明，
所有图像和其他媒体内容均根据知识共享署名-类似共享 4.0
国际许可证进行许可。要查看本许可证的副本…

</article>

[View on GitHub](https://github.com/ThomasTheSpaceFox/Zoxenpher)

[![Screenshot of zoxenpher](../Images/e43d60f716122b5c960592902fc7fc29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hRvSqe_A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5if5xunhqi8ua0o4wq7v.jpg) 

结合定制的 gopher 解析器/访问库，一些特定于实现的代码，以及 strazoloidwm 的强大功能，zoxenpher 是一个整洁的 gopher 客户端肯定，我能够很快获得基本的功能，因为许多繁重的工作已经由 strazoloidwm 处理。

它有完整的书签，窗口历史导航，图像，甚至声音类型支持。另外，使用 pygame 让我可以给它一点复古的外观，我认为这非常符合协议。