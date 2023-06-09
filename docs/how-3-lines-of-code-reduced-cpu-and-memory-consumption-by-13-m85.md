# 3 行代码如何将 CPU 和内存消耗降低 13%

> 原文：<https://dev.to/hdennen/how-3-lines-of-code-reduced-cpu-and-memory-consumption-by-13-m85>

或者假设如何破坏你的应用程序的性能。

我们有一个相当大的角度应用程序，使用 PixiJS 进行大量渲染。由于动画和画布区域的不断更新，我们不得不尽可能地优化，以保持应用程序在平板电脑上运行良好。

标准优化已经全部应用。

几乎所有组件都设置为`ChangeDetectionStrategy.onPush`，异步和广播使用`runOutsideAngular`，日志在生产中被抑制，等等。

我们利用了 PixiJS 纹理缓存，并且不对任何显示对象进行任何`.update()`调用，对`requestAnimationFrame`的`render()`调用在 angular 之外运行。

一个疏忽是当发生重大数据更改事件时，某些调用堆栈的大小，但除此之外，事情看起来相当有限。

然而，我们仍然有用户注意到性能问题。

对此有很多想法。使用 web workers 进行传输，在单独的调用堆栈中分配功能块以减少丢帧，让所有组件都使用`ChangeDetectionStrategy.onPush`，分离组件并重新连接以进行更新…

对角度和像素性能问题进行了大量调查。

然后，在调查角度绑定、RxJS 主题和角度绑定主题的内存和计时成本时，我创建了一个快速角度 CLI 应用程序。在设置好一切之后，我检查了性能记录，并注意到变更检测运行明显不足。

我应该在这里提到，为了保持我们的应用程序的帧速率体面，我们在一个循环中使用`requestAnimationFrame`来渲染 PixiJS 阶段。我们没有意识到的是，每个 RAF 调用都触发了变化检测。

但是为什么呢？

### 理解你的工具是如何工作的很重要

输入 Zone.js。我要求任何 Angular 开发人员解释 Zone.js 如何工作以及 Angular 如何利用 Zones。

下面是简短的版本:Zone.js 改变了浏览器 api 中所有异步调用的原型(`setTimeout`等)。)以便 a)上下文可以在调用栈之间共享，b)钩子可以在微、宏和事件任务结束时发出。

酷毙了。还有棱角？

Angular 创建(分叉)一个名为`NgZone`的新区域，它是根区域的子区域。这样，当发出那些任务结束挂钩时，angular 可以运行变化检测，以防某个调用导致某个地方的某个东西发生变化。当你没有太多组件的时候，这很好。当你有很多的时候就没那么多了。

我不是在批评 Angular 的变化检测。鉴于我们的应用程序的规模，它实际上做了令人印象深刻的工作。

这个问题与运行它所需的内存分配有关。我们最终每 900 毫秒收集 12Mb 垃圾。

好的，PixiJS 呢？

注册到 PixiJS 对象的任何事件监听器都不会影响浏览器 api。它通过它们的`InteractionManager`触及了 PixiJS 对事件的定制处理。并且是`InteractionManager`向文档注册了一个`pointermove`事件……这个文档已经被 Zone 打了补丁……被 Angular 分支了。

好的。因此，我们的第三方库在我们的框架中触发鼠标移动的变化检测——另一个问题——但这仍然不能解释是什么在每个动画帧上触发 CD。

*补充说明更新:我们使用`**Zone _ symbol**BLACK _ LISTED _ events `*解决了这个问题

### 现在严肃地说，理解你的工具是如何工作的是很重要的

PixiJS 有一个很好的方法来处理`DisplayObject`动画经过你的鼠标的场景。在正常情况下，你不会得到一个`mouseover`事件，因为鼠标没有移动，因此没有事件。

不过 PixiJS 很聪明。它使用那个`pointermove`事件来缓存最后一个指针事件。然后在 pixi 事件循环的下一个节拍，它将根据缓存的指针事件的位置检查移动的`DisplayObject`的位置，如果显示对象在鼠标下，则触发一个`mouseover`事件。

好的，Pixi 事件循环的下一个滴答？

原来，为了让他们所有的`DisplayObjects`都有动画效果，他们有一个共享的跑马灯在运行…等待它…作为`requestAnimationFrame`中的回调。

所以现在我们不仅有每秒 60 次的渲染循环，还有每秒 60 次的 Pixi ticker 触发变化检测。不给力。

一等奖是让 PixiJS 在根区域而不是 Angular 的`NgZone`中运行，但同时这 3 行代码停止了所有不必要的变更检测运行:

```
const ticker = Pixi.shared.ticker; 
ticker.autoStart = false; 
ticker.stop(); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在的垃圾收集量是以前的 1/3。