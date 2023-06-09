# 在 OpenEdge 中将计时器变成调度器

> 原文：<https://dev.to/patricktingen/turn-timers-into-a-scheduler-13j0>

DataDigger 是一款面向 OpenEdge 开发者的开源数据库浏览器。它使他们能够查看、更新、删除、即时通讯和导出数据库中的数据。DataDigger 完全是用 OpenEdge 4GL 编写的，有超过 40，000 行代码。里面是一些真正的宝藏，所以我将剖析数据挖掘者，揭示他们。今天:用调度器控制定时器。

在我之前的文章中，我解释了我如何使用定时器 ocx 来改善 OpenEdge 窗口的用户体验。简而言之:我用它来延迟浏览器上的值改变事件，以避免冗长的屏幕更新。

因为这很有效，我真的尝到了计时器的滋味，就像当你唯一的工具是锤子，每个问题看起来都像钉子，我的很多问题都需要计时器的形式来解决，所以我最终有了很多计时器:

*   要延迟表浏览中的值更改事件
*   2 秒钟后关闭弹出菜单
*   跟踪浏览器的水平滚动(没有相应的事件)
*   预加载数据库的缓存定义
*   为了保持与各种数据库的连接
*   调整可调整大小的数据字典的大小

虽然一切顺利，但感觉并不好:

[![9xkdeni](img/08590daeb1abda639b1e946cba6c54e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3WAqtEP6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/04/9xkdeni.png%3Fw%3D840)

注意到了吗？尤其让我恼火的是:

[![](img/0340b245fc6d4f49ba521d399ed97241.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VMPqphhi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/2w3zC8K.png)

连续六个定时器，虽然终端用户并不关心我的设计画布上有多少定时器，但这让我很困扰。我们——开发者——当然可以做得更好。

当我发现第七个计时器的用处时，我决定要适可而止；除了一个，我把所有计时器都拆了。我想要一个计时器来统治他们。但是为了统治他们，它需要把他们都集中在一个地方。在临时表上。当然了。我们的计划是将所有计时器事件保存在这个临时表中，并让我们的单个计时器跟踪要启动什么过程以及何时启动。

该表由一个过程字段(cProc)、一个时间间隔字段(iTime)和一个存储过程何时运行的字段(tNext)组成。

```
/* TT for the generic timer OCX */
DEFINE TEMP-TABLE ttTimer NO-UNDO 
  FIELD cProc AS CHARACTER 
  FIELD iTime AS INTEGER 
  FIELD tNext AS DATETIME 
  INDEX idxNext IS PRIMARY tNext 
  INDEX idxProc cProc. 
```

Enter fullscreen mode Exit fullscreen mode

请注意，tNext 是一个日期时间字段。这是一个千分之一秒的粒度，对于我们的目的来说已经足够精确了。还要注意，我们的主索引正好在那个字段上。我稍后将回到这一点。

好了，现在让计时器开始计时:

```
/* KeepAlive timer every minute */
RUN setTimer("KeepAlive", 60000). 
```

Enter fullscreen mode Exit fullscreen mode

那很容易，不是吗？好吧，这有点扯，让我们看看 setTimer 内部发生了什么:

```
PROCEDURE setTimer: 
  /* Enable or disable a named timer. */
  DEFINE INPUT PARAMETER pcTimerProc AS CHARACTER NO-UNDO. 
  DEFINE INPUT PARAMETER piInterval AS INTEGER NO-UNDO. 

  FIND ttTimer WHERE ttTimer.cProc = pcTimerProc NO-ERROR. 
  IF NOT AVAILABLE ttTimer THEN CREATE ttTimer. 

  ASSIGN 
    ttTimer.cProc = pcTimerProc 
    ttTimer.iTime = piInterval 
    ttTimer.tNext = ADD-INTERVAL(NOW, piInterval,"milliseconds"). 
  RUN SetTimerInterval.
END PROCEDURE. 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们所做的是在临时表中找到计时器，如果它不存在，就创建它。我们填充字段，最后运行 SetTimerInterval 过程。这个过程很巧妙:

```
PROCEDURE setTimerInterval:  
  /* Set the interval of the timer so that it will
   * tick exactly when the next timed event is due.
   */
  FOR FIRST ttTimer BY ttTimer.tNext:    
    chCtrlFrame:pstimer:INTERVAL = MAXIMUM(1,MTIME(ttTimer.tNext) - MTIME(NOW)).    
    chCtrlFrame:pstimer:ENABLED = TRUE.  
  END.
END PROCEDURE. 
```

Enter fullscreen mode Exit fullscreen mode

它只是根据执行时间找到第一条记录(这就是为什么我们在上面有索引)，我们从那个时间中减去“现在”，然后我们得到下一个事件之前的毫秒数。我们将其设置为定时器间隔，启用它，然后我们只需等待，直到定时器滴答作响。然后……

```
PROCEDURE pstimer.ocx.tick: 
  chCtrlFrame:pstimer:ENABLED = FALSE. 
  FIND FIRST ttTimer NO-ERROR. 
  IF AVAILABLE ttTimer THEN DO: 
    RUN VALUE(ttTimer.cProc). 
    IF AVAILABLE ttTimer THEN ttTimer.tNext = ADD-INTERVAL(NOW, ttTimer.iTime,"milliseconds"). 
  END. 
  RUN setTimerInterval.
END PROCEDURE. 
```

Enter fullscreen mode Exit fullscreen mode

我们找到第一个事件(主要索引在时间上)。我们运行代码，然后重新安排计时器。也就是说，如果 ttTimer 记录仍然可用。人们可以决定一旦任务运行，就没有必要再运行一次。如果我关闭菜单，我只需要在用户打开它时再次关闭它，所以在关闭菜单的过程中，我再次运行 setTimer，但这次使用参数' 0 '，这是删除它的标志。

**注意事项**

当然也有一些限制。您需要用计时器的名称定义一个过程，它不能处理参数。我想过引入这一点，但这会让事情变得过于复杂，所以我没有考虑。时间并不总是 100%准确的，如果您有两个过程应该同时运行，那么它们将按顺序运行。但是如果精确的计时不成问题，并且您可以不使用参数，那么这是改进应用程序 UI 的一个完美方法。

**免责声明**

DataDigger 中使用的代码比上面显示的要复杂一些。首先，它使用缓冲区而不是直接在 ttTimer 上操作，有更多的注释和一些用于边缘情况和调试的代码。保留所有代码会降低可读性，所以我干脆不写了。我做了一个小演示，没有不必要的代码，可以用作概念验证。你可以在 GitHub 上找到代码[。](https://github.com/patrickTingen/BlogSrc/tree/master/2018-04-26)

[![](img/b73179e27db63dd9e75c7fb3db37b6b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SWTpqDha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/U27Ofsq.png)

移动滑块来启动计时器，将它们移回零来禁用它们。第一个显示一个时钟，第二个是一个旋转的转子，第三个会在几秒钟后隐藏文本。

就是这样。让我知道你是否在自己的项目中使用过类似的技术，或者它是否可以改进。现在:玩得开心！