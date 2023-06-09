# 你的代码有多快？

> 原文：<https://dev.to/ngduc/how-fast-is-your-code-29l5>

编写代码的一个关键指标是速度。但是到目前为止，还没有方便的方法来有效地测量代码的速度。Chrome 和其他浏览器确实有 API(`new Date()`、`performance.now()`、`console.time`)，允许你把它们放在你的代码周围，然后你会做一个简单的计算来找出差异，比如:`t2 - t1`

### 另辟蹊径

当您想知道一个代码块执行了多长时间时，这可以满足您的即时需求。然而，当你想做更多的事情时，你将不得不写一些实用函数或库。

是一个高分辨率的计时器 API，你可以依靠它来测量时差。创建了许多模块来利用该 API，其中之一是 ms time-[https://github.com/ngduc/mstime](https://github.com/ngduc/mstime)-一个轻量级模块(2 KB gzip)，以毫秒为单位测量和可视化代码性能。

### 用法

现在，在`mstime`模块的帮助下，你可以编写这样的简单代码:

```
mstime.start("codeblock1");
myFunction();
console.log( mstime.end("codeblock1") ); 
```

到目前为止没有什么新东西。直到你仔细查看输出:

```
{
  entries: [
    { start: 41991120.53, end: 41991133.04, diff: 12.50 }
  ],
  plugins: { ... },
  last: 12.50,
  sum: 12.50,
  avg: 12.50
} 
```

打开机会之门！你现在可以控制每个测量的数据集，加上有用的计算，如:总和，平均值，最后一个值&一个插件系统！

有几个内置的插件，如:计算微调平均值(例如，获得 90%的数据)，将数据保存到本地存储，将数据绘制到图表等。

[使用 chartist 插件绘制时间数据的示例](https://rawgit.com/ngduc/mstime/master/docs/demo/index.html):

[![chartist plugin](img/758747206862167c56d94b9de55e4963.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RqSxVTMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ngduc/mstime/raw/master/docs/screenshot-01.png)

现在轮到您利用您捕获的数据集了！请在下面评论，让我知道你的想法、反馈或任何你想讨论的东西。