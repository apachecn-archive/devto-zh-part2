# RxJS 中的状态过滤器

> 原文：<https://dev.to/rkoutnik/stateful-filters-in-rxjs-3182>

我刚刚完成最后阶段的写作[用 RxJS](https://pragprog.com/book/rkrxjs/build-reactive-websites-with-rxjs) 构建反应式网站，将通过务实出版社出版。我是 RxJS 的超级粉丝，即使习惯起来有点复杂。为了揭开这个库的神秘面纱，我想在接下来的几周里浏览几个案例研究，并分析如何在现实世界的场景中使用它。

第一个案例来自我以前在一家网络安全公司的工作。这家公司提供了一项服务，他们可以在黑暗的网络上爬行(我仍然不能相信销售人员面无表情地这么说)，寻找客户设置的关键词。表面上，这个工具可以用来检测软件和/或内部文件的泄漏。为了实现这种扫描能力，我们需要构建一个可以动态更新的过滤器。

当使用 RxJS 编写软件时，要考虑的关键问题是将所有事情建模为随时间变化的事件流。如果您的应用程序不适合这种建模，RxJS 可能不适合您。幸运的是，这个任务很容易建模为两个独立的流。一个来自卡夫卡，是来自爬虫的新数据。另一个来自数据库轮询器，包含我们的过滤器需要测试的关键字。

我们将从代表这些流的两个神奇变量开始，因为处理卡夫卡超出了本文的范围。在变量的末尾附加一个美元符号是一种惯例，用来表明变量是可观察的。

```
crawlResults$;
latestKeywords$; 
```

Enter fullscreen mode Exit fullscreen mode

RxJS 已经有了一个内置的过滤操作符，所以这是可行的，至少在关键字列表更新之前是可行的。

```
crawlResults$
.pipe(
  filter(content => keywords.some(content.includes(keyword))
) 
```

Enter fullscreen mode Exit fullscreen mode

这个挑战的棘手部分是我们正在实现一个*有状态*过滤器。客户可以随时更新关键字列表。我们需要将该状态存储在某个地方。一种选择是创建一个关键字数组，并手动更新:

```
let filterKeywords = [];
// every five minutes
interval(5 * 60 * 1000)
.pipe(
  mergeMap(() => dbQuery())
)
.subscribe(newFilterKeywords => {
  filterKeywords = newFilterKeywords;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就让我们来处理状态和管理订阅。在这个例子中，我们需要确保订阅被正确地处理，否则我们可能会意外地创建大量的轮询器，用查询淹没数据库(这从来没有发生过，我不知道你在说什么，也不要相信我以前的同事说的话)。

RxJS 的明显优势是将一切都建模为流，但一个不太为人知的优势是 RxJS 提供了许多在框架内存储状态的方法，确保库不用担心订阅和过滤。在 RxJS 中将两个流组合在一起被称为“合并”,这里有多种合并操作符可以使用。大多数合并不维护内部状态，只是在获得数据时发送数据。这里，我们想知道来自两个流的每个事件的最新值，所以我们求助于`combineLatest`。`combineLatest`构造函数跟踪多个流，并根据来自任何流的事件，将所有流的最新值作为数组发出。

```
combineLatest(
  crawlResults$,
  latestKeywords$
) 
```

Enter fullscreen mode Exit fullscreen mode

我们快到了。剩下的一个问题是，每当`latestKeywords$`更新时，这也可能传递来自爬虫的最新点击。有一个操作符可以拯救我们:`distinctUntilChanged`，它是一个有状态过滤器，只传递与前一个值不同的值。加入一个`map`，从关键词中提取结果，如下所示:

```
combineLatest(
  crawlResults$,
  latestKeywords$
)
.pipe(
  filter(([results, keywords]) =>
    keywords.some(results.includes(keyword))),
  map(([results, keywords]) => results),
  distinctUntilChanged()
); 
```

Enter fullscreen mode Exit fullscreen mode

Tada！一个有状态的合并过滤器，将繁重的工作交给了 RxJS 库。如果你想了解更多关于 RxJS 的知识，包括构建许多功能示例，请查看[使用 RxJS](https://pragprog.com/book/rkrxjs/build-reactive-websites-with-rxjs) 构建反应式网站，目前处于测试阶段。