# 在 Web Worker 中构建带差异的异步 React 渲染器

> 原文：<https://dev.to/azizhk110/building-an-async-react-renderer-with-diffing-in-web-worker-217h>

从摆弄[反应调解器](https://github.com/facebook/react/tree/master/packages/react-reconciler)中学到的东西。

这是我在 [ReactFoo Delhi 2018](http://reactfoo.in/2018-delhi/) 的[演讲](https://reactfoo.talkfunnel.com/2018-delhi/26-create-your-own-custom-async-renderer-with-diffing)的文字记录+演讲者笔记。我在这里尝试一些新的东西，而不是分享幻灯片，让我知道哪个更好。

### 同步渲染演示

在我们开始什么是异步渲染之前，让我们看看同步渲染是什么样子的。

#### 演示:[azizhk.github.io/rrrww/](https://azizhk.github.io/rrrww/)

这是一个例子，我们要做的是努力提高它的感知性能。这是一个最坏的例子，它可能与真实世界的用例无关，但它将有助于理解数字上的差异。

[![](../Images/a3761c15b45bfc23904a82f44d306c68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RHnPWblu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIner5943a_8s1DW9P-sqMA.png) 

<figcaption>加载时同步渲染的 Chrome Devtools 时间轴记录</figcaption>

这就是同步渲染例子的时间轴记录。

快速参考，因为我会反复使用这个:

*   蓝色是 HTML 和 DOM 解析
*   黄色是 JavaScript 解析、编译、执行，一切与脚本相关的东西。
*   紫色是计算风格和布局。
*   绿色是颜料和合成物。

在我们的演示中，第一次完整的绘制发生在页面加载后 6 秒。

它描绘一切，包括文件夹下的内容。

你想要更好的首漆吗？可以使用服务器端渲染。时间表中会发生的是过程的顺序会改变。&紫色(布局)绿色(颜料)会发生在黄色(脚本)之前。因此，在页面变得可交互之前，这不会提高时间。

[![](../Images/e9fa26c49035f6a7172a8db2870fb5ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y6bNUHa7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APCEbAhJoW5V4uF7kimk26w.png)

<figcaption>Chrome Devtools Timeline 录制同步渲染时的交互性</figcaption>

当您在页面加载后与页面进行交互时，同步渲染会使网站冻结(无响应)20s。有些浏览器甚至会阻止滚动，甚至会冻结你的电脑/其他标签。

### 不要阻塞主线程

我们需要打破僵局。

我们想要 60fps

也就是 1000 毫秒除以 60。

这给了我们每帧 16ms 的预算。我们已经听过一次又一次了。

#### 但是我们真的需要把事情分开吗？

*   Chrome (Canary 以及未来版本的 Chrome)仍然允许滚动。这是因为这些层已经被绘制，并且翻译它们不需要等待主线程。滚动是已经绘制的层的简单组合。
*   CSS 转换动画仍然会运行，因为它们已经脱离了主线程。请看杰克·阿奇博尔德关于合成器线程的视频。

> ![](../Images/5eb282525e4c4083dbcf2832966a239b.png)![](../Images/4fe5ee27d6b61779e62235f21f29241a.png)V8@ v8js![](../Images/4d9c44713c216584b3d48ff3455cbb68.png)从 Chrome 66 开始， [@v8js](https://twitter.com/v8js) 在后台线程上编译 JavaScript 源代码，减少主线程编译时间高达 20%！[v8project.blogspot.com/2018/03/backgr…](https://t.co/zDR0crbNUn)2018 年 3 月 26 日下午 17:14[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=978319362837958657)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=978319362837958657)770[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=978319362837958657)1710

*   浏览器也通过把事情从主线程上移开来帮助我们。V8 首先将脚本解析移出了主线程，现在它也将脚本编译移出了主线程。这在加载时确实对我们有帮助，但在交互性方面不一定有帮助。

### 当页面有很多条目时，有时事情需要时间。_(ツ)_/

[![](../Images/81dc6537b76a7bbb70314befc2e2efee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mIzZ9cB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXhLbJzDfVybRJZxG1fBe1g.jpeg)

想想看，大量使用你的产品的人会积累数据，为了显示这些数据，你需要向 DOM 添加很多元素。这些是你的超级用户，分享你的产品，支持你的产品的用户。如果你的网站对他们来说不够快，他们肯定会转向下一个平台。所以无论你做什么，都不要让你的网站冻结在你的用户上。

### 异步渲染演示

有多种方法可以在主线程上分割工作。让我们来看看我们将要构建的一个。

网址:[https://azizhk.github.io/rrrww/async/](https://azizhk.github.io/rrrww/async/)

[![](../Images/a7abaac107f03b89672ad0e5d7c70ba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BHs79qaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWKBCNLNNku1UZ83fzSdZwQ.png) 

<figcaption>Chrome Devtools 时间轴记录加载时的异步渲染</figcaption>

对于我们的演示，在加载时，第一次绘制发生在 3.9 秒之前，而所有的绘制都在 20 秒完成。同样，在交互性方面，你可以看到事情在变化，而浏览器不会冻结在你身上，但总的时间会增加。

[![](../Images/03ed650de47ec8f024994643d80e7c3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TMwhEANZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Au4VJ4QEkcnWjKtlxSZgFPQ.jpeg)

因此，这是降低第一次油漆和总时间之间的权衡。但是还有一个额外的好处，就是可以将主线程释放出来，让其他任何可能想要加入并利用主线程的任务使用。

[![](../Images/846b35cb810ca90ec8450ee411dc95cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--skc0Lcat--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ai646-LLrGfG77bF0v_UqFQ.jpeg)T3】感知速度

因此，我们的目标是通过更快地显示内容的变化，同时不阻塞主线程，来提高对速度/性能的感知。

### 积木

我将简单介绍一下我们将要使用的一些技术。

*   网络工作者
*   RequestIdleCallback
*   反应和解
*   反应纤维
*   DOM 的数据结构

### 关键要点

*   使用 React API 创建您自己的渲染器
*   为 React 自己的异步渲染器做好准备
*   用 Web Workers 释放主线程

### 网络工作者

*   Javascript(语言)是单线程的。
*   平台提供 API 通过回调产生线程。
*   节点给你集群，浏览器给你网络工人

[![](../Images/68bca2f0cd94eb26de65a8b274da7164.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GCSghavr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuaBgvKzMnyUeeAU-OFmKUg.jpeg) 

<figcaption>网络工作者示意图。鸣谢:[海梅·冈萨雷斯](https://twitter.com/Vintharas)，[野蛮人遇上编码](https://www.barbarianmeetscoding.com/blog/2015/02/13/barbaric-basics-web-workers)</figcaption>

假设你在 worker.js

中有你的 worker 代码，你通过传递路径到 new Worker()调用从主线程初始化它。

```
const worker = new Worker('/worker.js')

// Send message to the worker
worker.postMessage({ ... })

worker.onmessage = ({data}) => {
  // Recieve message from the worker
} 
```

您可以使用 worker.postMessage.

向 worker 发送消息，并通过定义 worker.onmessage 函数接收消息。

如果你愿意，你也可以为同一个工作线程生成多个线程，但是我们只坚持一个。

```
self.onmessage = (message) => {
  // Receive message from main thread.
}

// Send message to main thread
self.postMessage({ ... }) 
```

同样，在 worker 上，您可以使用 on message 和 postmessage 发送和接收消息。

### requestIdleCallback

```
window.requestIdleCallback(function (deadline) {
  while (deadline.timeRemaining() > 0) {
    // do tasks
  }
}, {timeout: 100}) 
```

接下来是 requestIdleCallback。这有点像 setTimeout，但是我们没有指定浏览器何时调用我们的回调函数，而是给了它一个 lose reign 当主线程空闲时就给我打电话。

您还可以指定超时，即最大延迟。这有点像优先级，告诉浏览器，慢慢来，但不要超过 100 毫秒。

回调函数也会收到一个截止日期。截止时间目前设置为 50 毫秒，剩余时间()为您提供可用时间。之后，浏览器不会停止你的脚本或突然终止，但这只是一个帮助人们编写非阻塞代码的简单方法。

### 反应建议者

但在我谈论调解器之前，我想谈谈 React 15 和 React 16，什么发生了变化。您调用 setState，这将触发 React 的渲染过程。

[![](../Images/4460c23a60d99a1c0f082c9ca6a4d220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JpohvQ1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8U86I7c9AVHDy4vT1V3JiA.jpeg)

在 React 15 中，React DOM 将遍历虚拟 DOM 树，计算 diff，同时将 diff 修补到 DOM(浅绿色部分)。因为我们正在改变 DOM，这将触发布局和绘制。

现在我们的目标是释放主线程，要做到这一点，我们需要把事情分开。因此，使用这种方法，React 不能随时暂停。有人能想到这里会有什么问题吗？//提示它在时间轴中。

如果我们把 javascript 的执行按原样拆分，如果你修改了 DOM，布局就会被触发。因此，布局不是在 JS 执行结束时才被触发一次，而是在每次暂停后都被触发。

现在 React 已经无法知道布局会花多长时间或者如何最小化它的时间。

这是释放主线程和用布局反复阻塞它之间的权衡。

[![](../Images/e74f3074f51a298dcc9ed0fd89f973c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HotTvtd0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afzm8DXLn0U9CAwpLMQM0FA.jpeg)

现在 React 16 正在做的是遍历 VDOM，记录发生的任何变化，然后一次性应用这些变化。

记录变化的阶段称为渲染阶段。

应用这些变更的阶段称为提交阶段。

[![](../Images/aa81ba97e578364d2481c187fee72de0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UT28h_IE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AldKpaBJ3NJ0K7zgye8PM_Q.jpeg)

现在，因为我们在渲染阶段没有对 DOM 做任何事情，所以我们现在可以非常容易地将其拆分。这是 React 在未来版本中的发展方向。此外，如果我们过度使用了时间预算，它们还会提供一些钩子来中断提交阶段，我稍后会谈到这一点。

### 我的异步渲染器版本

但是让我们回到现在。

所以虽然 React 的下一个版本还在开发中。我想尝试使用 Web Workers 进行异步渲染。

[![](../Images/b0c17128461e67eef3f7ca0aaba200ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BEJYFIna--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXjXwrZZqSvOtiING9PB1Iw.jpeg)

因此，我们要做的是将不同的部分，即渲染阶段转移到 web worker 上。

并使用 postMessage 把需要打补丁的东西发给主线程。

这有助于我们释放主线程——至少在差异比较进行时。然后，我们还将使用 requestIdleCallback 的截止日期提供的时间来拆分提交阶段，目前还很幼稚。

[![](../Images/747b6086b66fff63af0728eae8ea641b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oS8HWl0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_-qGeUEAV3Fq-vKUCi6aFA.png)

#### 返回至反应调解器

因此，为了解释协调器，我们还需要了解什么是渲染器。

[![](../Images/9ca4c3f0926e472f042cb170ddee6788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bMxpaO2W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARn6GqvlHf6bRGnkt5KsSsA.jpeg) 

<figcaption>React 调解器与 React 渲染器</figcaption>

协调器处理 VDOM，比较不同的树，然后将更改发送到渲染器。

渲染器实际上与 UI 层通信。共享协调器时，设备可以有不同的渲染器。例如，您可以有两个呈现器，一个面向 DOM，另一个面向 DOM 中的 canvas 元素。这两个渲染器将使用不同的浏览器 API，但它们可以共享一个通用的协调器，这有助于它们确定差异以及需要对 DOM 应用什么。

#### 反应 DOM

[![](../Images/22abe43c6b8b2f6a574034058453d3af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1cmVWVT0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APUzbvMaGUqQ8FlHaN04Xtg.jpeg) 

<figcaption>反应 DOM 表示法</figcaption>

所以你使用同样熟悉的 React API 编写代码，你将创建扩展 React 组件的组件类等等。

本例中的渲染器:React DOM 是协调器和 DOM API 之间的桥梁。协调器获取您的类和组件，并为它们创建 VDOM。协调器会告诉 React DOM，这个元素被添加了，这个元素被删除了，这个属性被修改了。现在，React DOM 将查看属性，看它是属性、类还是事件侦听器，并调用相应的 DOM 函数。

#### 反应自然

[![](../Images/6c1bc474b6e5f9cd67aa77fa6b4b70b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dWfEV8QR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArOMis_8MXBECVUB3W-DwNw.jpeg)

类似地，对于 React Native，您可以用稍微不同的原语编写几乎相似的代码，但本质上是相同的 React API。

React Native 是 Reconciler 与原生 iOS 和 Android 之间的桥梁。

#### React 渲染器的优势

*   相同的声明式 API 来定义 ui。(即用户界面是数据的函数)
*   一次学习，随处书写

#### 野外的渲染器

*   反应 DOM
*   反应自然
*   反应艺术
*   反应小王国
*   反应画布
*   …以及更多。

#### 反应小王国

react-tiny-dom 是 react-dom 的一个非常小的实现，只包含 dom 元素、属性和特性，跳过了合成事件部分。它只有 200 行重要的代码。这是开始学习如何编写 react 渲染器的好地方，我将使用它的代码作为例子来介绍一些函数

[![](../Images/d5a1f2c333867136d47d6e03a00e0c71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2-RQ8dLw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ajf372-pGs9AB2KLd8OQq9Q.jpeg) 

<figcaption>跳水运动员跳水。照片由 [Rye Jessen](https://unsplash.com/photos/StOeOi3h16M?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在[Unsplash](https://unsplash.com/search/photos/diving?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T8】上拍摄</figcaption>

### 我们开始吧

```
import Reconciler from 'react-reconciler';
const WebWorkerRenderer = Reconciler({
  // host config
}); 
```

我们从“react-reconciler”导入协调器，然后向其传递一个主机配置对象。这个主机配置只是一个回调钩子的集合，用于协调器向呈现器传递有关 VDOM 变化的信息。

```
const hostConfig = {
  supportsMutation: true,
  now: Date.now,
  getRootHostContext: () => {},
  prepareForCommit: () => {},
  resetAfterCommit: () => {},
  getChildHostContext: () => {},
  shouldSetTextContent: () => {},
  createInstance: () => {},
  createTextInstance: () => {},
  appendInitialChild: () => {},
  finalizeInitialChildren: () => {},
  appendChildToContainer: () => {}
} 
```

我将检查主要的回调挂钩，而其他的可以作为无操作。

```
createInstance(type, props, root, context, interInstance) {
  return document.createElement(type);
},

createTextInstance(text, root, interInstance) {
 return document.createTextNode(text);
}, 
```

首先我们创建了一个实例和一个文本实例。在这里，您可以创建 UI 底层组件实例。您的组件创建的每个元素都将调用这些函数。在 tiny-dom 的情况下，它调用 document.createElement 和 document.createTextNode

//现在举个例子，如果你有一个 three.js 渲染器，它会在这里创建 shape 元素，D3.js 渲染器会创建 svg 元素等等。

```
appendInitialChild(parentInstance, child) {
  parentInstance.appendChild(child);
},

appendChildToContainer(parentInstance, child) {
  parentInstance.appendChild(child);
}, 
```

然后调用 appendInitalChild，这样就可以将实例添加到其父实例中。你的容器是你的顶层 div，你想在其中呈现你的组件。appendChildToContainer 是将实例添加到顶级容器的地方。对于 DOM 渲染器来说，appendInitialChild 和 appendChildToContainer 是相同的，但是对于画布渲染器来说，它们可能是不同的。其中容器是 canvas 元素，但在 appendInitialChild 中，parentInstance 可能是一个组。

```
finalizeInitialChildren (domElement, type, props) {
  Object.keys(props).forEach(propName => {
    const propValue = props[propName];      
    // Apply each prop to the domElement
    // For DOM these properties can be 
    // style, className, eventListeners or attributes
  });

  // Return true if anything needs to be done
  // after it has been committed.
  // commitMount function will be called after mounting.
  return false;
}, 
```

然后，finalizeInitialChildren 是将您的道具应用到您刚刚创建的 domElement 的地方。在这里，你检查每一个属性，检查它的类型，它的样式或者类名，或者它是否是一个事件监听器，或者它是否是一个简单的 HTML 属性。

同样如果你需要在元素被提交后做些什么，你可以返回 true。

我所说的提交，是指当它已经显示在屏幕上的时候。对于 DOM 呈现器，提交意味着它已经被添加到 DOM 树中，并且已经触发了布局。

```
commitMount(domElement, type, props, interInstance) {
  domElement.focus();
}, 
```

假设您在 finalizeInitialChildren 中返回了 true，那么在将元素添加到 DOM 后，将调用 commitMount 函数

。

这个函数的用例的一个很好的例子是，如果你需要在元素被添加后自动聚焦，你在 finalizeInitialChildren 中检查自动聚焦属性，然后在 commitMount 函数中聚焦它

### 命令

#### 渲染阶段:

1.  `createInstance`，`createTextInstance`
2.  `appendInitialChild`(对于新树的子节点)
3.  `finalizeInitialChildren`

首先创建您的元素，然后将它们添加到它们的父元素中，前提是父元素还不是 DOM 树的一部分，即父元素也是在当前的修补过程中创建的。

然后道具被添加到元素上。

#### 提交阶段

1.  `appendChildToContainer`、`appendInitialChild`(添加到 DOM 的顶层节点)
2.  `commitMount`(安装后，如果`finalizeInitialChildren`返回 true)

然后在提交阶段，它们被添加到 DOM 中，如果在它们被提交后需要做什么。

[![](../Images/059742464700ba6d5acfbdb5facc4f87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ad8gAgdk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcPaWMsJYGPap7wn6-u_K2A.jpeg) 

<figcaption>海星代表突变。照片由[塞缪尔·博尔多](https://unsplash.com/photos/lGiWWYqjyHc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/@s_bordo01?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T8】拍摄</figcaption>

### 变化

因此，要通知 reconciler 您的渲染器支持突变，您必须将 supports mutation 标志设置为 true。

#### 订单变更

一种类型的变异是元素的重新排序，为了处理这种情况，协调器给出了三个钩子，append、insertBefore 和 removeChild

所以我们在这里只调用较低级别的 DOM 函数，它们也是同名的。

```
appendChild(parentInstance, child) {
  parentInstance.appendChild(child);
},

insertBefore(parentInstance, child, beforeChild) {
  parentInstance.insertBefore(child, beforeChild);
},

removeChild(parentInstance, child) {
  parentInstance.removeChild(child);
}, 
```

因为这也可以在容器级别，所以我们也有相应的容器级别函数。appendChildToContainer，insertInContainerBefore 和 removeChildFromContainer

```
appendChildToContainer(parentContainer, child) {
  parentContainer.appendChild(child);
},

insertInContainerBefore(parentContainer, child, beforeChild) {
  parentContainer.insertBefore(child, beforeChild);
},

removeChildFromContainer(parentContainer, child) {
  parentContainer.removeChild(child);
}, 
```

#### 属性/特性改变

在**渲染阶段**你准备比较，创建一个已经改变的属性列表。

```
prepareUpdate(domElement, type, oldProps, newProps) {
  const uniqueProps = new Set([...Object.keys(oldProps), ...Object.keys(newProps)]);
  const changedProps = Array.from(uniqueProps).filter(
    propName => oldObj[propName] !== newObj[propName]
  );
  return changedProps;
}, 
```

在 commitUpdate 函数中，您可以应用这些更改。prepareUpdate 中返回的内容是 commitUpdate 中得到的第一个参数。

```
commitUpdate(domElement, changedProps, type, oldProps, newProps, internalInstanceHandle) {
  changedProps.forEach(propName => {
    // Set changed attributes to domElement
 });
}, 
```

### 插头反应组件

所以我们看到了创造和突变。现在如何将我们的 React 组件传递给这个渲染器？因此，您创建了一个呈现函数，它接受组件和一个目标 div，并在那里将组件传递给协调器。

```
export function render(element, domContainer, callback) {
  let root = domContainer._reactRootContainer;

  if (!root) {
    const newRoot = Renderer.createContainer(domContainer);
    root = domContainer._reactRootContainer = newRoot;
  }

  return Renderer.updateContainer(element, root, null, callback);
}

render(<App />, document.getElementById('root')) 
```

这里，render 函数在元素上创建一个简单的缓存，如果它已经有一个 react 树启动或没有启动，并对它调用 updateContainer。

### 现在让我们使它异步

[![](../Images/b07c1e3219c3656dd9078e4a5f550ac2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u7yzVjSu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASlFBUEMfdeL0-kpMfz50GQ.jpeg)

这是我们将要建造的东西的草图。我们的 Web Worker 将发送初始的 DOM 结构。当一个用户事件发生时，我们将该事件发送给一个动作创建者，该创建者创建一个动作有效负载发送给工作者。工人消耗这些有效载荷。在这里，我使用 redux，因此有效负载作为一个动作被分派到 reducer，它改变存储状态并触发重新呈现。

[![](../Images/b8c7b7ed7b8d34ce876cc918b708007a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oXThjf99--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhSuYaLG71Il84iLO77c9eQ.jpeg)T3】主机配置

我们看到了正确的主机配置。

[![](../Images/57b8312c5ae5fa8ef3ad6dc5f9ae6668.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FhuW7k5_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZ-pXWLSGLFqBX-Rm5QE3NQ.jpeg)

我们要做的是做两个配置，一个在 Web Worker 上，一个在主线程上。Web Worker 配置将被插入到协调器中，当其函数被调用时，它将创建一个参数注释，并将这些注释进行批处理，并将它们发送到主线程。

[![](../Images/0e39650f4ba87c3e39d83bbaf37f4207.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Om89rqgO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjfxGFzmt6sEJCc9FWjARgA.jpeg)

这是我们将在 Web Worker 中创建的元素的对象表示。他们有类型、道具和孩子等。(暂时没有事件处理程序)

[![](../Images/29e3ae0989acc80910d773084fa056ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U6DhOpjY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADimnabIckNGB948hTZ3kWg.jpeg)

但不幸的是，在变异过程中，当协调器提供父对象的引用时，我们不能与主线程进行相同的通信，因为对象是通过值传递的，所以每次创建新对象时。

[![](../Images/3ec171c9bf0505fdf44bff39b7a16cf6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o8g41UvR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWXfh7YuEhzxN6eQcplbcHA.jpeg)

因此，我们为每个创建的对象分配一个唯一的标识。在向客户端发送消息时，我们使用这个唯一的 id 来引用元素。“uuid/v4”给了我们一个 16 个字符长的 id，我们可以命名它们，这也有助于我们在服务器端渲染。

### 事件？

交流事件是困难的。这是我们不能即插即用每个 React 组件的地方。因此必须实现一些自定义逻辑。回到我们的图表。

[![](../Images/b07c1e3219c3656dd9078e4a5f550ac2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u7yzVjSu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASlFBUEMfdeL0-kpMfz50GQ.jpeg)

将整个事件从主线程发送给工作线程是很困难的，因此我们将在主线程上定义动作创建者，它将获取事件，动作创建者将提取动作所需的任何基本信息，并通过 post 消息发送给工作线程。

[![](../Images/d17267933afb05e599e2be3cae089ad2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--50JhW1CJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJGa1oYI2IBXmyWEK_KZxdg.jpeg)

因此，定义一个 data-onclick，它定义了我们希望触发的动作创建者。

[![](../Images/8eab0ad5388ff2bf5af3ae227dce7f59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ewGwRI8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ayp4KeG8tCF4THg8404Bp1g.jpeg)

我们有一个全球行动创建者对象。它获取被分派的事件，从该事件中，您可以提取目标、其属性以及创建要发送给 worker 的动作有效负载所需的任何内容。(我自己不喜欢，扩展性不好，即使拆分多个文件也不支持树抖动。)

[![](../Images/510f46d6eef84b0aeb378f9349a8fba4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R5XBnJR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgrmQ7OKpG-E-YU8M7nS6vQ.jpeg)

这就是添加事件监听器并连接 actionCreators 和 worker 的方法。我使用[委托](https://www.npmjs.com/package/delegate)进行事件委托。这不是渲染器的一部分，只是因为它不直接接触协调器，但可以是使用该渲染器所需的样板文件的一部分。

### 树遍历

[![](../Images/bcd61f31d56e551d17fec8f30d0074ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y1xMHLsp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHsVZAaHZqUqWn0994plF1Q.jpeg) 

<figcaption>照片由[阿达什·库姆穆尔](https://unsplash.com/photos/zThTy8rPPsY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/@akummur?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

最后一步是提交阶段主线程中的树遍历。

[![](../Images/19d58f6f61aee50b2fd74a00d5619cd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aHhN56Ad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5QElPjCIAq8yuTp1fFmuzQ.jpeg)

在提交阶段，我们已经从 Web Worker 收到了 DOM 对象表示，如果我们需要在提交阶段暂停这个过程，当我们接近最后期限时，我们需要保存我们暂停的堆栈位置。(deadline 来自我们之前看到的 requestAnimationFrame。)

现在这将变得很乏味，如果我们每次暂停前都保存堆栈位置，那么当我们继续时，我们将需要时间来展开到这个堆栈位置。这也是脸书的 Sebastian Markbage 在纤维原理文件中预先警告的。他说这一代堆栈和倒回你停下来的地方，可能会占用你很少的 50 毫秒预算的很多时间。

[Fiber 原则:参与 Fiber 问题#7942 facebook/react](https://github.com/facebook/react/issues/7942)

[![](../Images/acc2c1b30a418b480544af5f465f417a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zsf1aYJE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoxcOoW8WHMUJOCMBlVd3LQ.jpeg)

因此，我们不保存堆栈位置，而是改变遍历的方式，这样我们只需要保存我们所在的元素，而不是整个堆栈位置。所以你已经从 Ankit 那里听说遍历算法已经改变了，这就是它被改变的原因。

此外，该文档还用于遍历 VDOM 以计算差异，但我们将这些相同的原则应用到提交阶段。

这里每个节点定义了你需要去哪里。首先你遍历到它的孩子，如果它没有任何孩子，你去它的兄弟姐妹，如果它没有兄弟姐妹，你去它的父母的兄弟姐妹，等等。

所以现在对于这个新的遍历，**数组将不能工作**。

[![](../Images/903f095dfd9471c13517e889c15c35f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XnNchBlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADnA3ZLEjRQeSFbMmPW6Pfg.jpeg)

所以你不用数组，而是为你的孩子使用一张地图。

每个子节点都有自己的唯一标识符，它的父节点、下一个兄弟节点、上一个兄弟节点、第一个子节点、最后一个子节点。

所有这些都是为了让你可以在 O(1)中做 appendChild、insertBefore 和 removeChild

### 成果演示:

[https://azzhk . github . io/rrrww/async/](https://azizhk.github.io/rrrww/async/)

### 源代码:

[https://github.com/azizhk/rrrww/tree/async_initial](https://github.com/azizhk/rrrww/tree/async_initial)

### 骗局

我们没有看到这些想法成为主流是有原因的。

[![](../Images/3c21c699ce3db8902bbbdb0273f87155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jJcxZ6Xa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXkUxlSQXgr1DLPbX4LBJmQ.jpeg) 

<figcaption>照片由[马里·莱扎瓦](https://unsplash.com/photos/q65bNe9fW-w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/@marilezhava?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

*   VDOMs 需要三倍内存
*   无法读取 DOM 维度
*   序列化开销
*   状态不一致
*   无优先级(提交阶段按时间划分，不基于优先级)

#### 那又怎样？

我不想把这看作一次失败，而是一次经验的积累。未来还有更多值得期待的。

### 未来的 React 版本

以下是 react 未来版本的预期。

*   **异步渲染**
*   **工作的优先顺序**
*   基于拉动的方法，React 为您做出决策。
*   当你决定优先顺序时，也要采取推进方式

[![](../Images/52a609ceb123063edc758d43ed12e171.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nMFLG8V3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGj3Sd-O_ZlrrZg4RLWlOVg.jpeg)

什么是基于拉动的优先化方法？所以 react 会知道发生了什么类型的突变。下面是 React 如何定义副作用类型的列表。并且基于某种启发，它可以自己决定应该呈现哪些组件的优先级。有些组件可能只是修改了回调函数，可能是因为它们是在渲染函数中定义的，所以可能不会真正修改 DOM。和 react 可以降低它们相对于其他组件的优先级。

[![](../Images/bdb5898789d2ead18771f33a1d910460.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k-o62qGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0S96v63o2WgDx2nKALBH0g.jpeg)

基于拉的优先化的另一个例子是基于已经发生的事件的类型。假设您想要定义要处理的事件的优先级，并以同样的方式对这些事件引起的副作用进行优先级排序。你的事件也由 React 处理，你使用 onClick，onChange 等。因此它知道哪个事件的处理程序调用了 setState，并且可以跟踪副作用并相应地对它们进行优先级排序。

[![](../Images/105ee341a9e25fccee7b98f7f3aebde8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tm0aKTOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGsOhxOXjZ9hQIOLzRrAc6g.jpeg)

基于推送的优先化是您告诉 react 如何优先化组件的地方。到目前为止，我们还不知道任何 API 可以精确定义组件的数字优先级，但是有一种方法可以告诉 react 对特定的 react 树进行去优先级化。您可以使用 React.unstable_AsyncMode 来包装您的低优先级组件树。

### 其他值得期待的想法:

#### **react-native-dom**

所以在最后，我还想提到一个伟大的项目，它是由 Vincent Reimer 创作的。我们采用了将 React DOM 转移到 Web Worker 的方法。他的方法基本上是采取反动的土办法；默认情况下是异步的；并将其移植到 DOM。

[vincentrimer/react-native-DOM](https://github.com/vincentriemer/react-native-dom)

#### 工人世界

最近由 Google 的 AMP 团队开源的 worker-dom 复制了 worker 线程上的所有低级 dom 函数，并使您能够在没有任何复杂设置的情况下将应用程序移动到 worker。他们甚至将 DOM 事件传递给 Web Worker 线程。太酷了。

[ampproject/worker-dom](https://github.com/ampproject/worker-dom)

就这样结束了。我很匆忙地写了这篇文章，因为我一直在拖延，想把它完成，然后继续我的下一篇博客。因此，如果您发现任何错误、建议、重复的文本，请务必写一个便条，我会修复它。

### 关键要点

*   推动异步渲染
*   创建您自己的渲染器
*   释放主线程
*   用 Web Workers 释放主线程

#### 更多参考:

1.  [https://engineering . hex acta . com/di dact-fiber-incremental-reconcing-B2 Fe 028 DC AEC](https://engineering.hexacta.com/didact-fiber-incremental-reconciliation-b2fe028dcaec)
2.  [https://github.com/koba04/react-fiber-resources](https://github.com/koba04/react-fiber-resources)
3.  [https://medium . com/@ agent _ hunt/hello-world-custom-react-renderer-9 a 95 b 7 CD 04 BC](https://medium.com/@agent_hunt/hello-world-custom-react-renderer-9a95b7cd04bc)