# 使用声明性数组提高可读性

> 原文：<https://dev.to/bnevilleoneill/writing-for-readability-with-declarative-arrays-4igf>

[![](img/d51d679ddae0433a2dfecabc66a1c622.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vU6AQc9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/1%2APFDFHEJZiETncPczyBVJOQ.jpeg)

在过去的几年里，JavaScript 经历了一些重大的变化。ES6 的广泛采用和现代框架的兴起已经将前端行业的焦点转移到更具声明性的方法上。

命令式编程侧重于计算机运行的命令。声明性关注于你想从你的计算机中得到什么。虽然命令式方法通常更接近金属，因此性能更高，但除非您正在处理大型数据集，否则这种优势可能可以忽略不计。

通过以声明的方式操作和消化数组，您可以生成可读性更好的代码。

这里有几种方法可以做到这一点。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 声明式数组方法

### [T1。减少](#reduce)

也许最强大的数组方法是. reduce。它通过对数组的每一项调用一个提供的函数来工作。这个回调函数最多接受四个参数(尽管我发现自己通常只使用前两个):

1.  previousValue，通常被称为“累加器”。这是上次调用回调时返回的值
2.  currentValue，它是数组中的当前项
3.  currentIndex，它是数组中当前项的索引
4.  数组，这是被遍历的完整数组

除了这个回调之外，该方法还接受一个可选的初始值作为参数。如果未提供初始值，将使用数组中的第一个值。

一个非常简单的例子是获取一组数字总和的简化器。

[https://medium . com/media/3ea 72 f 18 AC 0 D1 c 8215 ebb 42660 F3 d 157/href](https://medium.com/media/3ea72f18ac0d1c8215ebb42660f3d157/href)

回调将当前值添加到累加器中。因为没有提供初始值，所以它从数组中的第一个值开始。

### [T1。地图](#map)

。类似地，map 将接受针对数组中的每个元素调用的回调。

这个回调接受三个参数:currentValue、currentIndex 和数组。

map 方法返回一个与原始数组长度相等的数组，而不是跟踪累加器。回调函数将原始数组的值“映射”到新数组中。

简单地图回调的一个例子是返回每个数字的平方。

[https://medium . com/media/689453707 aa 58635 b 2896407 FCA 11 bef/href](https://medium.com/media/689453707aa58635b2896407fca11bef/href)

### [T1。过滤器](#filter)

。筛选器接受与. map 具有相同参数的回调。筛选器回调应该返回“truthy”或“falsey”值，而不是像. map 那样“转换”数组中的每个值。如果回调返回真值，那么该元素将出现在新数组中。

一个例子可能是检查一列数字是否能被 3 整除。

[https://medium . com/media/879d 74 e 08 a 0 f 59 a 708 Fe 3 F4 b 51 b 531 da/href](https://medium.com/media/879d74e08a0f59a708fe3f4b51b531da/href)

### 可读回调技巧

### 1。说出你的复试

这可能是数组方法可读性最大的一次提升。通过命名数组方法回调，您可以立即提高可读性。

比较这两个:

[https://medium . com/media/c 9 D2 c 9 aa 972 b 53 ECB 0 CBF 267 BF 7a 0 AAA/href](https://medium.com/media/c9d2c9aa972b53ecb0cbf267bf7a0aaa/href)

通过为回调命名，您可以立即更好地理解代码试图完成什么。命名时，有几件事要记住。

**保持一致**。有一个好的命名约定。我喜欢把我所有的。减少和。将回调映射到任何对象。如果我把一组数字简化成一个和。

如果我将用户对象的数组映射到名称，则为 toFullName。使用时。filter，我喜欢把我的回调命名为 isWhatever 或者 isNotWhatever。如果我只筛选出完美的正方形，那就是完美正方形。

**要简洁。**理论上，你的回访应该只做一项工作——试着用一个描述性但简短的名字来描述这项工作。

### 2。命名您的回调参数

像 accumulator 和 currentValue 这样的名字在编写代码时很容易找到——它们是如此的通用，以至于永远不会出错。然而，因为它们是如此的通用，它们对代码的读者没有帮助。

进一步扩展一下——如果您正在操作一个对象数组，并且只使用几个值，那么在参数列表中使用对象析构可能更具可读性。

[https://medium . com/media/d2a 70788 cf 4475 c 43d 486 Fe 750d 57 b4b/href](https://medium.com/media/d2a70788cf4475c43d486fe750d57b4b/href)

### 3。选择正确的方法

之前我提到过。reduce 可能是最强大的数组方法。这是因为，由于其累加器的概念，它在返回什么方面是无限灵活的。map 必须返回一个与原始长度相等的数组。A .filter 必须返回其原始。与。减少你能做的一切。地图和。过滤器功能和更多功能...所以为什么不总是使用。减少？

你应该使用。地图和。过滤*是因为*对它们的限制。当你的代码的读者看到一个. filter 时，他们会知道它将返回一个子集，但是如果他们看到一个. reduce，他们可能需要查看回调才能知道这一点。使用最具体的工作方法。

### 4。将小功能链接在一起

到目前为止，大部分的例子都相当自然地展示了每一个例子是如何工作的。这里有一个更类似于现实生活场景的例子:获取一组对象，类似于您可能从 API 接收到的内容，并对它们进行格式化，以便在您的应用程序上使用。

在这种情况下，假设我们从一个 API 接收附近餐馆的选择。

[https://medium . com/media/8644 ed 8 ACC 8853 f 80220 BDB 32616870d/href](https://medium.com/media/8644ed8acc8853f80220bdb32616870d/href)

我们希望通过在我们的网站上创建一个列表来消化(双关语)这些数据，该列表列出了附近所有目前正在营业并提供食物的餐馆。

实现这一点的一种方法是通过单个大型减速器。

[https://medium . com/media/b0ab 167 aed 1344953 ba 20 ef 504 E4 bde 0/href](https://medium.com/media/b0ab167aed1344953ba20ef504e4bde0/href)

然而，这个缩减器做三件事:检查是否打开，检查它是否是一个有效的机构(不是 coffee)，并映射到名称。

下面是用单一目的回调编写的相同功能。

[https://medium . com/media/221 C2 b27 E6 a 1420338103 babf 1227139/href](https://medium.com/media/221c2b27e6a1420338103babf1227139/href)

将您的功能分成多个回调还有其他一些好处。如果您的任何过滤器的逻辑发生了变化，您可以很容易地准确隔离出需要发生这种变化的地方。您还可以在其他地方重用某些回调的功能(例如，您可以过滤到 isOpen 和 isPizza)。

这种方法也使得测试变得更容易——你可以为你所有的构建模块编写单元测试，当你添加新的功能时，你只需要重用这些模块，而不需要担心任何东西被破坏。

### **结论**

命令式和声明式都有它们的位置。如果您正在处理大量数据，并且每毫秒都很重要，那么坚持使用 while 和 for 循环。不管怎样，这就是幕后发生的事情。

我认为在大多数情况下，代码可读性(以及可维护性)是值得权衡的。通过有意识地使用这些回调，您可以最大限度地提高收益。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *