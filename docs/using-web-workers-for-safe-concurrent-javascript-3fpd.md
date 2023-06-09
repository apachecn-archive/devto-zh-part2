# 使用 web workers 实现安全、并发的 JavaScript

> 原文：<https://dev.to/bnevilleoneill/using-web-workers-for-safe-concurrent-javascript-3fpd>

[![](img/0a85a37600414b18f2747c45da90a280.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e6eTClpZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvsgQ0hOZkxQFfKqjKOWbYw.png)

Web workers 提供了一种在浏览器的单线程执行之外运行 JavaScript 代码的方法。单线程处理显示内容的请求以及通过键盘、鼠标点击和其他设备的用户交互，还处理对 AJAX 请求的响应。

事件处理和 AJAX 请求是异步的，可以被认为是在通用浏览器显示的代码路径之外运行一些代码的一种方式，但它们仍然在这个单线程中运行，并且确实必须相当快地完成。

否则，浏览器中的交互性会停止。

Web Workers 允许 JavaScript 代码在一个单独的线程中运行，完全独立于浏览器线程及其通常的活动。

近年来，关于网络工作者到底有什么用处，有很多争论。如今，CPU 的速度非常快，几乎每个人的个人电脑都有几千兆字节的内存。类似地，移动设备的处理器速度和内存大小已经接近台式机。

曾经被认为是“计算密集型”的应用程序现在被认为没有那么糟糕。

### 你说这不是真空是什么意思？

但是很多时候，在决定如何高效地执行代码时，我们只考虑在开发环境中测试的一个应用程序的执行。在用户手中的现实生活系统中，许多事情可能同时执行。

因此，在隔离状态下运行的应用程序可能不需要使用工作线程，但却有必要使用它们来为广大用户提供最佳体验。

启动一个新的 worker 就像指定一个包含 Javascript 代码的文件一样简单:

[https://medium . com/media/54f 116 b 9159 f 391940 D2 175629 ef 406/href](https://medium.com/media/54f116b9159f391940d2d175629ef406/href)

一旦创建了工作线程，它就在独立于主浏览器线程的单独线程中运行，执行脚本中提供给它的任何代码。浏览器相对于指定 javascript 文件的当前 HTML 页面的位置进行查找。

使用 Javascript 代码中的两个互补特性在工作线程和主 Javascript 线程之间传递数据:

*   发送方的 postMessage()函数
*   接收端的“消息”事件处理程序

与其他事件处理程序一样，消息事件处理程序接收一个事件参数；这个事件有一个“数据”属性，它包含从另一端传来的任何数据。

这可以是双向通信:主线程中的代码可以调用 postMessage()向工作线程发送消息，而工作线程可以使用在工作线程环境中全局可用的 postMessage()函数的实现将消息发送回主线程。

Web Worker 中的一个非常简单的流程如下所示:在页面的 HTML 中，一条消息被发送给 Worker，页面等待响应:

[https://medium . com/media/c 12 B2 Bab 56 DC 91432 e 763 D8 f 65 CD 7498/href](https://medium.com/media/c12b2bab56dc91432e763d8f65cd7498/href)

工作代码等待一条消息:

[https://medium . com/media/45a 95685 ACF 3c 88 Fe 4161 b 5730d 546d 0/href](https://medium.com/media/45a95685acf3c88fe4161b5730d546d0/href)

上面的代码将把它打印到控制台:

```
Message posted from webworker: Worker running Message posted from webworker: Worker received data: {“data”:”123456789"} 
```

### 工人被期望长寿，而不是停止和开始

在工作者的生命周期中，浏览器和工作者之间可以发送和接收多条消息。

web workers 的实现以两种方式确保了安全、无冲突的执行:

*   工作线程的独特、隔离的全局环境，独立于浏览器环境
*   postMessage()调用中主线程和工作线程之间的数据传递交换

每个工作线程都有一个独立的全局环境，不同于浏览器页面的 JavaScript 环境。工作人员根本无权访问页面的 Javascript 环境中的任何内容——无论是 DOM，还是“窗口”或“文档”对象。

Workers 对某些东西有自己的版本，比如用于将消息记录到开发人员控制台的“console”对象，以及用于发出 AJAX 请求的 XMLHttpRequest 对象。但是除此之外，在 worker 中运行的 Javascript 代码应该是自包含的；主窗口想要使用的来自工作线程的任何输出都必须通过 postMessage()函数作为数据传递回来。

此外，任何通过 postMessage()传递的数据在被传递之前都会被*复制*，因此在主窗口线程中更改数据不会导致工作线程中的数据发生变化。这为在主线程和工作线程之间传递的数据的冲突并发更改提供了内在的保护。

### 网络工作者的用例

web worker 的典型用例是任何在其执行过程中可能变得计算昂贵的任务，要么消耗大量 CPU 时间，要么花费不可预测的大量时钟时间来访问数据。

网络工作者的一些可能的使用案例:

*   预取和/或缓存数据以备后用
*   轮询和处理来自 web 服务的数据
*   大型数据集的处理和显示(想想基因组学)
*   游戏中与移动相关的计算
*   图像处理和过滤
*   处理文本数据(代码语法、拼写检查、字数统计)

CPU 时间是简单的用例，但是对资源的网络访问也非常重要。很多时候，互联网上的网络通信可以在几毫秒内执行，但有时网络资源变得不可用，直到网络恢复或请求超时(可能需要 1-2 分钟才能清除)才停止。

即使在开发环境中单独测试时，一些代码可能不需要很长时间就能运行，但当多个项目同时运行时，在用户环境中运行可能会成为一个问题。

下面的例子演示了使用 web workers 的几种方法。

### 演示:游戏碰撞检测

(系好安全带。这是个很长的例子。)

现在，在网络浏览器中运行的基于 HTML5 的游戏随处可见。游戏的一个核心方面是计算游戏环境各部分之间的运动和交互。一些游戏有相对较少的活动部分，并且相当容易制作动画([超级马里奥模拟器](http://supermarioemulator.com/mario.php)克隆，有人吗？).但是让我们考虑一个计算量更大的情况。

这个例子包括大量的彩色球在一个矩形的边界上弹跳。目标是将球保持在游戏的边界内，并且检测球之间的碰撞并使它们相互弹开。

边界检测执行起来相对简单和快速，但是碰撞检测可能需要更多的计算，因为它大致随着球的数量的平方增长:对于“n”个球，每个球必须与每个其他球进行比较，以查看它们的路径是否相交并需要被反弹(导致 n 乘以 n，或 n 的平方比较)。

因此，对于 50 个球，大约需要进行 2500 次检查；对于 100 个球，需要进行 10000 次检查(实际上略少于这个数量的一半:如果你检查球 n 和球 m，你不需要稍后检查球 m 和球 n，但是仍然会涉及大量的计算)。

在这个例子中，边界和碰撞检测是在一个单独的工作线程中完成的，该线程以浏览器动画的速度执行，每秒 60 次(每次调用 requestAnimationFrame())。定义了一个世界对象，它保存了一个球对象的列表；每个球对象都知道它的当前位置和速度(以及半径和颜色，以允许它被绘制)。

在当前位置绘制球发生在主浏览器线程中(它可以访问画布及其绘制上下文)；更新球的位置发生在工作线程中。如果球碰到游戏边界或与另一个球碰撞，速度(特别是球的运动方向)会更新。

世界对象在浏览器中的客户端代码和工作线程之间传递。即使对于几百个球来说，这也是一个相对较小的对象(100 乘以每个球大约 64 字节的数据= 6400 字节的数据)。所以这里的问题是计算量。

这个例子的完整代码可以在 codepen [这里](https://codepen.io/bwilln/pen/VXYRay)找到。有一个 Ball 类来表示正在被动画化的对象，还有一个 World 类实现 move()和 draw()方法来制作动画。

如果我们在不使用工人的情况下制作纯动画，主要代码将如下所示:

[https://medium . com/media/f6c 40876938 c 84253377 e 16 DD 08 DFD 0 b/href](https://medium.com/media/f6c40876938c84253377e16dd08dfd0b/href)

该代码使用 requestAnimationFrame()在显示器刷新期间每秒运行 60 次 animationStep()函数。动画步骤包括“移动”，更新每个球的位置(可能还有方向)，然后是“绘制”，用新位置的球重新绘制画布。

为了在这个应用程序中使用工作线程，游戏动画循环的“移动”部分(World.move()中的代码)将被移动到工作线程中。世界对象将通过 postMessage()调用作为数据传递给工作线程，以便在那里进行 move()调用。世界对象显然是要传递的东西，因为它有球的显示列表和它们应该待在其中的矩形边界，每个球都保留了关于其位置和速度的所有信息。

由于使用了 worker，修改后的动画循环如下所示:

[https://medium . com/media/ce aad 1 fc 8d 65 f 33 b 134d 6 abdc 9 e 8 f 728/href](https://medium.com/media/ceaad1fc8d65f33b134d6abdc9e8f728/href)

工作线程本身看起来就像这样:

[https://medium . com/media/616 f 4d 9 c 423 ad 7950 dcba 253069 DD 455/href](https://medium.com/media/616f4d9c423ad7950dcba253069dd455/href)

这里的代码依赖工作线程从主代码接受 postMessage()中的世界对象，然后将世界传递回主代码，并更新位置和速度。请记住，当世界对象传入和传出工作线程时，浏览器会制作一份副本—这里的假设是，制作世界对象副本的时间远远少于 O(n**2)碰撞计算(它实际上是“世界”中保存的相对较少的数据)。

运行基于新工作线程的代码会导致意外错误:

```
Uncaught TypeError: world.move is not a function at collider-worker.js:10 
```

原来，在 postMessage()调用中复制对象的过程将复制对象上的数据属性，*而不是对象的原型*。世界对象的方法在被复制并传递给 worker 时被从原型中剥离。这是[“结构化克隆算法”](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)的一部分，这是在主线程和 web worker 之间复制对象的标准方式，也称为[序列化](https://www.w3.org/TR/html5/infrastructure.html#safe-passing-of-structured-data)。

为了解决这个问题，我将在 World 类中添加一个方法来创建其自身的新实例(该实例将具有包含这些方法的原型),并从消息中传递的数据中重新分配数据属性:

[https://medium . com/media/AEF 82 b 783 aa 6949990 ea b49 c 9208 B4 d 1/href](https://medium.com/media/aef82b783aa6949990eab49c9208b4d1/href)

尝试用这个修复运行动画会导致另一个类似的错误…世界显示列表中的底层球对象也必须被恢复:

```
Uncaught TypeError: obj1.getRadius is not a function at World.checkForCollisions (collider.js:60) at World.move (collider.js:36) 
```

必须增强世界类的实现，以便从数据中恢复其显示列表中的每个球，以及世界类本身。

现在，在世界级的比赛中:

[https://medium . com/media/59924177d 196d 976647 bcb5 a 3271 cf 35/href](https://medium.com/media/59924177d196d976647bcb5a3271cf35/href)

以及在 Ball 类中实现的类似 restoreFromData()方法:

[https://medium . com/media/5933 f 343 c8 F3 a 765 b 24 b 0 AFC 1 ea ADB 91/href](https://medium.com/media/5933f343c8f3a765b24b0afc1eaadb91/href)

这样，动画可以正确运行，计算工作线程中数百个球的“移动”，并在浏览器中以每秒 60 次的速度显示它们的更新位置。

这个工作线程的例子是计算受限的，而不是内存受限的。如果记忆也是一个问题呢？

### 演示:设定图像阈值

最后一个例子，让我们看一个 CPU 和内存都很密集的应用程序:获取 HTML5 画布图像中的像素并转换它们，生成并显示另一个图像。

这个演示将使用 Ilmari Heikkinen 在 2012 年编写的[图像处理库](https://www.html5rocks.com/en/tutorials/canvas/imagefilters/)。它将获取一幅彩色图像，并将其转换为二进制黑白图像，以中间灰度值作为阈值:灰度值小于该值的像素显示为黑色，大于该值的像素显示为白色。

阈值代码遍历每个(rgb)值，使用一个公式将其转换为灰度值:

[https://medium . com/media/12381 C4 c8 2b 6280 cc 18 C9 d 137 F4 b 4387/href](https://medium.com/media/12381c4c82b6280cc18c9d137f4b4387/href)

对于最初看起来像这样的图像:

[![](img/9cafbc3882129f2273bfad19b5d9bb47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R4yziyU4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/350/1%2AamhA820xUBT0Stc8c7umpw.jpeg)

阈值算法产生如下两种色调的黑白图像:

[![](img/3975c54ed6d57a9d5378fd6539470b33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--awf5FvYD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/350/1%2AcCw35ziRauVMzBbDv9NJ6w.png)

这个演示的代码笔可以在[这里](https://codepen.io/bwilln/pen/RMKwMX)找到。

即使对于小图像，所涉及的数据和计算也可能很大。一个 640×480 的图像有 307，200 个像素，每个像素有四个字节的 RGBA 数据(“A”代表 alpha，或透明数据)，使图像数据的大小达到 1.2 MB。计划是使用一个 web worker 迭代每个像素，并将它们转换为新的 RGB 值。图像的像素数据将从浏览器传递到工作线程，修改后的图像将被返回。每次在客户端和工作线程之间来回传递数据时，最好不要复制这些数据。

对 postMessage()调用的扩展提供了一种方法来指定与消息一起传递的数据的一个或多个属性，这些属性应该通过引用传递，而不是复制。看起来是这样的:

[https://medium . com/media/f8e 7 adba 2 EAC 07 ffcf 3 AC 7 da 677357 b 0/href](https://medium.com/media/f8e7adba2eac07ffcf3ac7da677357b0/href)

任何实现可转移接口的对象都可以在这里指定。ImageData 对象的“data.buffer”符合这一要求，它属于 Uint8ClampedArray 类型(用于存储 8 位图像数据的数组类型)。ImageData 是 HTML5 canvas 上下文对象的 getImageData()方法返回的内容。

一般来说，有几种标准数据类型实现了可转移接口:ArrayBuffer、MessagePort 和 ImageBitmap。ArrayBuffer 又是由一些特定的数组类型实现的:Int8Array、Uint8Array、Uint8ClampedArray、Int16Array、Uint16Array、Int32Array、Uint32Array、Float32Array、Float64Array。

因此，如果现在数据是通过引用而不是通过值在线程之间传递的，那么数据可以在两个线程中同时被修改吗？这些标准防止了这一点:当数据通过 postMessage()传递时，在发送端对数据的访问被禁用(规范中实际上使用了术语“中性的”)，使其不可用。通过 postMessage()将数据再次传递回来，在工作线程端“中和”了它，但在浏览器中又可以访问它。这个“阉割”特性是在 Javascript 引擎中实现的。

### 总结和最后的想法

HTML5 Web workers 提供了一种方法，可以将繁重的计算卸载到一个单独的执行线程中，而不会停止浏览器的主事件线程。

两个例子展示了网络工作者的一些特征:

*   通过 postMessage()调用和“消息”事件侦听器进行基本的双向消息传递
*   计算密集型示例:HTML5 动画中的边界和碰撞检测
*   最后一个例子:图像阈值处理(计算和数据密集型)，演示在 postMessage()函数中通过引用传递大量数据

在此过程中，所演示的示例探讨了 web workers 的几个问题和实现细节:

*   在 postMessage()中传递 Javascript 对象时应用的序列化过程不会复制对象原型中的方法——必须设计一些代码来恢复这些方法
*   当从 getImageData()方法传递像素数据数组时，必须将像素数据对象的 buffer 属性传递给 postMessage()调用(类似于 imageData.data.buffer，而不是 imageData.data) —正是缓冲区实现了 Transferable

目前大多数主流浏览器都支持 Web workers。Chrome、Safari 和 Firefox 大约从 2009 年开始支持它们；MSEdge 支持它们，从 IE10 开始，Internet Explorer 也支持它们。

为了与浏览器兼容，可以简单地检查“if (typeof Worker！== "undefined ")'可以保护创建和使用工作线程的代码，同时在工作线程之外(在超时或动画帧中)交替执行相同的代码。

### Plug: LogRocket，一款用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *