# 在开发中利用 Javascript 控制台的力量

> 原文：<https://dev.to/worldclassdev/leveraging-the-power-of-the-javascript-console-in-development-24ap>

作为一名软件开发人员，您很可能会被层出不穷的开发工具淹没。甚至我也挣扎了一段时间，试图选择最适合我的开发工作流程的。在大多数情况下，我的开发环境中充斥着我从来没有用过的工具，或者大部分工具都没有得到充分利用，因为它们只是被闲置在那里。

经过多年低效的开发和不断的重新发明轮子，我发现了一个我曾经忽略的非常强大的工具。每个软件开发者和互联网用户都默认拥有一个“Javascript 控制台”。“那是 **console.log** 对吧？我对游戏机相当熟悉。”但你是吗？

> 在本文中，我们将仔细探索控制台对软件开发人员有所帮助的大量应用程序。

## 什么是控制台？

javascript 控制台是现代浏览器中的内置功能，它在一个类似外壳的界面中提供了开箱即用的开发工具，允许开发人员:

*   查看网页上出现的错误和警告的日志。
*   使用 javascript 命令与网页交互。
*   调试应用程序并直接在浏览器中遍历 DOM。
*   检查和分析网络活动

基本上，它让你能够在浏览器中编写、管理和监控 Javascript。

### 在浏览器中启动控制台

javascript 控制台是内置的，可以通过以下浏览器中的键盘快捷键轻松访问:

*   对于 Mozilla Firefox

> **对于 MAC 用户:** *COMMAND + OPTION + K*
> 
> **对于 Windows 和 Linux 用户:** *CTRL + SHIFT + K*

*   对于谷歌浏览器

> **对于 MAC 用户:** *COMMAND + OPTION + J*
> 
> **对于 Windows 和 Linux 用户:** *CTRL + SHIFT + J*

*   对于谷歌浏览器

> **对于 Windows 用户:** *F12*

*   为了狩猎

> **对于 MAC 用户:** *COMMAND + OPTION + C*

*   为了迷你歌剧

> **对于 MAC 用户:** *COMMAND + OPTION + I*
> 
> **对于 Windows 和 Linux 用户:** *CTRL + SHIFT + I*

你应该有一个这样的界面，很可能停靠在底部

[![](../Images/637f5613ae9cd6f34bbcc6e156e5424d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p7mFpZVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/AX9TO2AQUOQq7t4mlGdq_console.png)

## 探索控制台 API

在开发过程中，开发者在调试过程中掌握**安慰**自己的艺术是非常重要的。让我们探索一下使用控制台 API 的一些好处。there☺有很多未开发的潜力

### Console.log

这可能是所有方法中最常用的方法。它会在控制台中显示一条消息。当您将一个或多个对象传递给此方法时。每个对象都被计算并连接成一个空格分隔的字符串。

```
 console.log( "The current time is:", Date.now()); 
```

Enter fullscreen mode Exit fullscreen mode

**这会将相应的消息打印到控制台，如下所示:**

[![](../Images/dc6e8f4b6855584cdd745cdc705eae4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9dPc0uhJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/LsLFpdZTG2tampKsSEFe_consoledotlog.png) 
这个类似于**的 console.debug()** 。

### 控制台

此方法有助于您对控制台中的日志进行分组。在 console.group()之后和 console.groupEnd()之前出现的所有控制台输出在视觉上组合在一起。例如，

```
 function name(obj) {
      console.group('Country Profile');
      console.log('name: ', obj.name);
      console.log('continent: ', obj.continent);
      console.log('type: ', obj.type);
      console.groupEnd();
    }

    name({"name":"Nigeria","continent":"Africa","type":"Democratic"}); 
```

Enter fullscreen mode Exit fullscreen mode

在有许多日志组的情况下，调用 console.groupCollapsed 而不是 console.group。这将以折叠的默认模式显示组。

**输出应该是这样的** [![](../Images/a531a4abf64a94d164a8730fd73dd023.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uwvt6Gb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/SJwmdadwRJevQzXizryd_consoledotgroup.png)

### 控制台. trace

这个方法只是从调用它的地方打印一个堆栈跟踪。这有助于您跟踪代码的执行。由于这超出了本文的范围，[您可以通过这个链接](https://developer.mozilla.org/en-US/docs/Web/API/console#Stack_traces)了解更多关于堆栈跟踪以及它们是如何工作的

### 控制台.表

基本上，这有助于将表格数据(通常是一个数组)显示为一个表格。

```
 let food = [
      { name: "Rice", class: "carbohydrate" },
      { class: "protein", name: "Beans" }
    ];

    console.table(food); 
```

Enter fullscreen mode Exit fullscreen mode

**输出:** [![](../Images/6e56be4e09913eae9c161fedd44ef523.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lhSN5zIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/CZBgP7uaRMiKDRstst9Q_consoledottable.png)

### 控制台.错误

此方法输出错误信息。与来自 **console.log()** 的消息不同，它将消息样式化为一个错误，并包括一个调用方法的堆栈跟踪。

```
 console.error("error: can't identify file type"); 
```

Enter fullscreen mode Exit fullscreen mode

这类似于 **console.exception()** 和 **console.warn()** 。

### 控制台.计数

这将计算并输出 count()在同一行和同一标签上被调用的次数。它能够识别应用程序中任何地方的标签。

```
 function insert(city) {
      console.count(city + 'registered');
    } 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

[![](../Images/7e5d64d8b11509125746421d32d7651c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NAZHQuic--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/UyPB0ArLRKagyZeY7CGn_consoledotcount.png)

### 控制台.时间和控制台.时间结束

**Console.time** 使用作为输入参数指定的名称启动计时器，并且在给定页面上可以同时运行多达 10，000 个计时器。一旦启动，我们使用一个对 console.timeEnd()的调用来停止计时器并将经过的时间打印到控制台。

```
 console.time('total');
    console.time('init arr');
    var arr = new Array(10000);
    console.timeEnd('init arr');
    for (var i = 0; i < arr.length; i++) {
      arr[i] = new Object();
    }
    console.timeEnd('total');
    // init arr: 0.0546875ms
    // total: 2.5419921875ms 
```

Enter fullscreen mode Exit fullscreen mode

### 控制台

这将清除控制台

```
 clear(); 
```

Enter fullscreen mode Exit fullscreen mode

### 控制台.断言

如果求值表达式为 false，此方法会将消息和堆栈跟踪记录到控制台。

```
 function lesserThan(a,b) {
      console.assert(a< b, {"message":"a is not lesser than b","a":a,"b":b});
    }
    lesserThan(5,6); 
```

Enter fullscreen mode Exit fullscreen mode

**输出:** [![](../Images/81a98b29d395c3554606be20499cb57f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4L378bG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/57596/yxhQeMeTQ8yp7cTJgQL7_consoledotassert.png)

## 用控制台进行的常见操作

控制台对于开发中的大量功能来说非常方便。其中包括:

*   诊断并记录到控制台
*   计时和监控执行
*   处理异常和错误
*   监控事件
*   评估表达式
*   数据对象的比较

## 结论

通过 javascript 控制台可以有效地执行无数的操作，这使得它成为集成到您的开发工作流中的一个好工具。掌握一些工具比让这么多未充分利用的工具塞满你的工作空间要好。

## 有用的资源

请通过下面的链接继续了解如何利用这项出色的技术:

*   [Chrome 开发工具上的谷歌开发者文档](https://developers.google.com/web/tools/chrome-devtools/console/)
*   [Mozilla 开发者网络网络文档](https://developer.mozilla.org/en-US/docs/Web/API/Console)
*   [数字海洋社区教程](https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-developer-console#working-with-the-console-in-a-browser)