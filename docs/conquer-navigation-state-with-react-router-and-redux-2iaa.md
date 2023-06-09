# 用 React-router 和 Redux 征服导航状态

> 原文：<https://dev.to/bnevilleoneill/conquer-navigation-state-with-react-router-and-redux-2iaa>

[![](img/07ae39a3d9cca82f5ea6beb7732c7895.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FKfqOIwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApB345V4AkkJlNIiT_wO76g.png)

传统应用程序和单页面应用程序的一个基本组成部分是导航——能够从一个页面移动到另一个页面。

好吧，那又怎样？

等等我！

在本文中，我不仅将向您展示在 React/Redux 应用程序中导航的细微差别，还将向您展示如何以声明的方式做到这一点！您还将了解如何在应用程序的导航开关之间维护状态。

准备好了吗？

> 👉注意:在这篇文章中，我假设你对 Redux 的工作原理有一个很好的理解。如果没有，你可能想看看我关于[理解 Redux](https://medium.freecodecamp.org/understanding-redux-the-worlds-easiest-guide-to-beginning-redux-c695f45546f6) 的文章📕。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 我们将要使用的应用程序

为了尽可能实用，我为此建立了一个简单的应用程序。

下面这张 GIF 是 *EmojiLand 的。*

<figure>[![](img/ffc7abef379b75778f714c2694d47535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TlNg1oYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUorZ-5kajXJZ5yUHnzBepQ.gif) 

<figcaption>你好，EmojiLand！</figcaption>

</figure>

EmojiLand 是一个简单的应用程序，但足以帮助你理解我将在本文中分享的非常重要的提示。

请注意该应用程序如何停留在当前路线上，但当单击按钮时，它会执行一些假操作，并在完成假操作后重定向到另一条路线。

在现实世界中，这个假动作可能是获取某些资源的网络请求，或者任何其他异步动作。

为了让我们达成共识，让我分享一下 *EmojiLand* 应用程序是如何构建的。

### 快速概述 *EmojiLand* 是如何建造的

要继续工作，从 GitHub 获取应用程序的 [repo。如果你觉得懒，可以跳过。](https://github.com/ohansemmanuel/nav-state-react-router)

克隆回购:`git clone`[https://github.com/ohansemmanuel/nav-state-react-router.git](https://github.com/ohansemmanuel/nav-state-react-router.git)

移动到目录:`cd nav-state-react-router`

安装依赖项:`yarn install`或`npm install`

然后运行应用程序:`yarn start`或`npm start`

完成了吗？

该应用程序是一个基本的 redux 设置反应。还包括一个非常简单的 react-router 设置。

在`containers/App.js`中，您将找到该应用程序中包含的 6 条路线。

下面是完整的代码表示:

```
const App = () => (
  <Router>
    <Switch>
      <Route exact path="/" component={AngryDude} />
      <Route path="/quiet" component={KeepQuiet} />
      <Route path="/smile" component={SmileLady} />
      <Route path="/think" component={ThinkHard} />
      <Route path="/thumbs" component={ThumbsUp} />
      <Route path="/excited" component={BeExcited} />
    </Switch>
  </Router>
); 
```

Enter fullscreen mode Exit fullscreen mode

每条路线都指向一个表情符号组件。`/quiet`渲染`KeepQuiet`组件。

下面是`KeepQuiet`组件的样子:

```
import React from "react";
import EmojiLand from "../components/EmojiLand";
import keepQuietImg from "img/keepquiet.png";
import emojiLand from "./emojiLand";
const KeepQuiet = ({ appState, handleEmojiAction }) => (
    <EmojiLand
      EmojiBg="linear-gradient(120deg, #a6c0fe 0%, #f68084 100%)"
      EmojiImg={keepQuietImg}
      EmojiBtnText="Keep Calm and Stay Quiet."
      HandleEmojiAction={handleEmojiAction}
      appState={appState}
    />
  );
export default emojiLand(KeepQuiet); 
```

Enter fullscreen mode Exit fullscreen mode

呈现 EmojiLand 组件的是简单的功能组件。EmojiLand 组件的构造在`components/EmojiLand.js`中。

这没什么大不了的，你可以在 GitHub 上看一看。

重要的是，它接受一些道具，如背景渐变、图像和按钮文本。

更精致的是出口组件。

请看一下上面代码块的最后一行。

```
export default emojiLand(KeepQuiet); 
```

Enter fullscreen mode Exit fullscreen mode

emojiLand 右边有一个高阶分量。它所做的只是确保当你点击任何表情组件中的一个按钮时，[它会模拟一个虚假的动作](https://github.com/ohansemmanuel/nav-state-react-router/blob/master/src/containers/emojiLand.js)大约 1000 毫秒。请记住，实际上这可能是一个网络请求。

emojiLand 高阶组件通过将 appState 属性传递到 emoji 组件中来实现这一点。在这个例子中，保持安静

当第一次呈现任何表情组件时，appState 是一个空字符串，`""`。大约 1000 毫秒后，appState 更改为`DO_SOMETHING_OVER`。

其中`DO_SOMETHING_OVER`表示为常数，如下所示:

在常量/动作类型:

```
export const DO_SOMETHING_OVER = "DO_SOMETHING_OVER"; 
```

Enter fullscreen mode Exit fullscreen mode

现在，这就是这个应用程序中每个表情符号组件的工作方式！

还要记住，在每条路线上，都会呈现一个单独的 EmojiLand 组件。

[![](img/ebf19eed6b4408088f020013362833a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6PXFG51v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN5Ql1tcxFpsbgewbeqGc0Q.png)

[![](img/dff9692d84be086393e3f48f03365366.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T9Ckjfqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAFFoi8P_xc-d1tT2wBVZWQ.png)

<figure>[![](img/942ab98ba5b2d5cac80f7b5afbe9d890.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eXJE5VJh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2WCdVoU8-UTAVqc8xMqQog.png) 

<figcaption>愤怒的，兴奋的，&安静的</figcaption>

</figure>

[![](img/fd3c3b021c49364ac732c27d6c737ee7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pncafb5w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvaLmQVdLDS90uv5ve5IL6Q.png)

[![](img/3b9490363302a661a61f301210c1fab1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VApipZbn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AURBRM8rpWYy8LT0cLDAU2Q.png)

<figure>[![](img/89b75320e1f7a1d911ac8e5f56f85532.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rxcGbjhx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxIfTgz00ICn0CPS55E8UAw.png) 

<figcaption>SmileLady，ThinkHard，& ThumbsUp</figcaption>

</figure>

### 像反应冠军一样改变方向

假流程完成后，让我们假设您想要在 *EmojiLand* 应用程序中重定向/移动到另一个路由。

你是怎么做到的？

首先，记住在回家的路上，渲染的是愤怒的成分。

<figure>[![](img/ebf19eed6b4408088f020013362833a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6PXFG51v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN5Ql1tcxFpsbgewbeqGc0Q.png) 

<figcaption>这个 AngryDude 分量十足。</figcaption>

</figure>

处理重定向的更具声明性的方法是使用 React-router 中的重定向组件。

让我告诉你怎么做。

因为我们想从 AngryDude 组件进行重定向，首先，像这样在 containers/AngryDude.js 中导入重定向组件:

```
import { Redirect } from "react-router-dom"; 
```

Enter fullscreen mode Exit fullscreen mode

为了使重定向工作，它必须像常规组件一样呈现。在我们的特定示例中，当 appState 持有值`DO_SOMETHING_OVER`时，我们将进行重定向，也就是说，假动作已经完成。

现在，这是代码:

```
const AngryDude = ({ appState, handleEmojiAction }) => {
    return appState === DO_SOMETHING_OVER ? (
<Redirect to="/thumbs" />
    ) : (
      <EmojiLand
        EmojiBg="linear-gradient(-180deg, #611A51 0%, #10096D 100%)"
        EmojiImg={angryDudeImg}
        EmojiBtnText="I'm so pissed. Click me"
        HandleEmojiAction={this._handleEmojiAction}
        appState={this.props.appState}
 /> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果 appState 等于`DO_SOMETHING_OVER`，则呈现重定向组件。

```
<Redirect to="/thumbs" /> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，required to prop 被添加到重定向组件中。这个道具需要知道**重定向到哪里。**

有了这些，下面是实际操作:

<figure>[![](img/3b3595f728a66aaa4f60742e14566178.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l07J-1pz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ar4G04AQ-qnRx-P4XcRjLDA.gif) 

<figcaption>从“生气”转向“竖起大拇指”</figcaption>

</figure>

如果我们继续对其他路由组件进行同样的操作，我们就可以成功地重定向所有路由！

这就是实际情况:

<figure>[![](img/ffc7abef379b75778f714c2694d47535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TlNg1oYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUorZ-5kajXJZ5yUHnzBepQ.gif) 

<figcaption>通过所有路线重新定向！</figcaption>

</figure>

很简单，对吧？

但是有一点问题，我将在下一节中解决这个问题。

### 避免重定向替换历史中的当前路线

我将打开一个新的浏览器并点击应用程序，但在某些时候，我会尝试返回，即使用后退浏览器按钮。

请看下面:

<figure>[![](img/dbf91579f8c202674f7e21b97f57c43f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6LRMMIY4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxjX7RqKgK5LdyrZ8_D06DA.gif) 

<figcaption>试图“返回”将我带回浏览器主页:(</figcaption>

</figure>

请注意，当我单击后退按钮时，它不会返回到以前的路线，而是将我带回浏览器的主页。

为什么？

这是因为默认情况下，使用重定向组件将替换浏览器历史堆栈中的当前位置。

所以，即使我们循环了多条路线，这些路线也在浏览器的“记录”中互相替换。

对于浏览器，我们只访问了一条路线。因此，点击后退按钮把我带回了主页。

这就像有一个数组，但不是推送到数组，而是替换数组中的当前值。

不过，有一个解决办法。

重定向组件可以采用取消这种行为的推送属性。使用 push prop，每条路由都会被推送到浏览器的历史堆栈中，不会被替换。

下面是它在代码中的样子:

```
return appState === DO_SOMETHING_OVER ? (
    <Redirect push to="/thumbs" />
  ) : (
    <EmojiLand
      EmojiBg="linear-gradient(-180deg, #611A51 0%, #10096D 100%)"
      EmojiImg={angryDudeImg}
      EmojiBtnText="I'm so pissed. Click me"
      HandleEmojiAction={handleEmojiAction}
      appState={appState}
    />
  ); 
```

Enter fullscreen mode Exit fullscreen mode

这是结果。

<figure>[![](img/b23916658a750947a605ebb2405f4e7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bRlt5X3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVJucGLA-Dw59KhBIMI1jBA.gif) 

<figcaption>现在，点击返回按钮就可以正常工作了:)</figcaption>

</figure>

请注意我们现在如何导航回以前访问过的路线！

### 保持导航状态

当您从一条路线移动到另一条路线时，前一条路线中的变量不会被带到下一条路线。他们走了！

是的，走了，除了你做一些工作。

有趣的是，重定向组件使这变得非常容易。

与将字符串传递给 prop 到 Redirect 相反，您也可以传入一个对象。

<figure>[![](img/424b812c5e2e1daa06886accc616a0bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YCgns7za--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmXJJnCS65sbv19Z3Ts39Sw.png) 

<figcaption>你也可以将一个对象传递给重定向组件</figcaption>

</figure>

有趣的是，有了对象表示，还可以传入一个状态对象。

在 state 对象中，您现在可以存储任何您希望带到被重定向到的路由的键值对。

<figure>[![](img/edc939941234aee620bed00a6f03f266.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pow5KdZz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnkFps36oyZXKtI3keaN9bg.png) 

<figcaption>在“至”道具内添加状态对象。</figcaption>

</figure>

让我们看一个代码示例。

当从 AngryDude 组件重定向到 ThumbsUp 时，让我们将一些值传入 state 字段。

以下是我们之前拥有的:

```
<Redirect push to="/thumbs" /> 
```

Enter fullscreen mode Exit fullscreen mode

那要改成这样:

```
<Redirect
      push
to={{
        pathname: "/thumbs",
        state: {
          humanType: "Cat Person",
          age: 12,
          sex: "none"
        }
      }}
    /> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我已经传入了 3 个不同的键值对！人类类型、年龄和性别。

但是在重定向到/thumbs 路由时，我如何接收这些值呢？

对于路由组件，react-router 提供了特定位置属性。在这个位置属性中，您可以像这样访问状态对象，location.state 或 this.props.location.state

> 注意:路由组件是由 react-router 组件渲染的组件。他们通常在签名中，

这里有一个例子，我记录了在新的 route/thumbs 中接收到的状态对象，即在新呈现的 Thumbs 组件中。

```
const ThumbsUp = ({ appState, handleEmojiAction, location }) => {
console.log(location.state);
  return appState === DO_SOMETHING_OVER ? (
    <Redirect push to="/quiet" />
  ) : (
    <EmojiLand
      EmojiBg="linear-gradient(-225deg, #DFFFCD 0%, #90F9C4 48%, #39F3BB 100%)"
      EmojiImg={thumbsUpImg}
      EmojiBtnText="You rock. Thumbs up!"
      HandleEmojiAction={handleEmojiAction}
      appState={appState}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意 location prop 是如何被解构的，然后是 console.log(location.state)。

经过重定向和开发控制台检查后，状态对象确实就在那里！

<figure>[![](img/7c1674d2cb4a75904765b956413d61d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--teETcfKq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ayv5aTY_Qerf1wAhSvSdMyw.png) 

<figcaption>状态对象接收并登录到新的`/thumbs`路线！</figcaption>

</figure>

您甚至可以更进一步，基于传入的状态实际呈现一些 UI 组件。

我是这样做的:

<figure>[![](img/550ab6b162e52d067bc06314d75d529c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9CkMI5mb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASF0KSkH449j_nTisYceWpQ.png) 

<figcaption>看看按钮下面的文字。这些值是从位置状态对象中获取的！</figcaption>

</figure>

通过获取传递给 ThumbsUp 的状态，我对其进行了映射，并呈现了按钮下方的值。如果你关心我是如何做到的，看看 [components/EmojiLand.js](https://github.com/ohansemmanuel/nav-state-react-router/blob/master/src/components/EmojiLand.js) 中的源代码

现在我们已经取得了一些不错的进展！

### 任何真实世界值？

你可能一直在想，“是的，这很酷，但是在现实世界中我在哪里使用它呢？”

有许多用例，但一个非常常见的是在表格中呈现结果列表。

但是，该表中的每一行都是可单击的，单击某一行时，您会希望显示有关所单击值的更多信息。

您可以使用这里的概念重定向到新路由，还可以将一些值从表行传递到新路由！所有这些都是通过利用 to prop 中重定向的状态对象来实现的！

### 但是，还有另外一种解决方法！

在开发领域，通常有多种方法来解决一个问题。我希望这篇文章尽可能实用，所以我将向您展示在路线之间导航的另一种可能的方法。

假设我们想在执行某个操作后从/thumbs 路由重定向到安静路由。在这种情况下，我们希望在不使用重定向组件的情况下完成**。**

你会怎么做？

与我们呈现重定向组件的前一个解决方案不同，您可以使用下面显示的稍微更强制性的方法:

`history.push("/quiet)`或`this.props.history.push("/quiet")`

好吧，但是这个历史物体是从哪里来的？

就像上一个例子中的位置一样，react-router 也将历史属性传递给路由组件。

以下是我们在 containers/Thumbs.js 中的内容:

```
const ThumbsUp = ({ appState, handleEmojiAction, location }) => {
  return appState === DO_SOMETHING_OVER ? (
    <Redirect push to="/quiet" />
  ) : (
    <EmojiLand
      EmojiBg="linear-gradient(-225deg, #DFFFCD 0%, #90F9C4 48%, #39F3BB 100%)"
      EmojiImg={thumbsUpImg}
      EmojiBtnText="You rock. Thumbs up!"
      HandleEmojiAction={handleEmojiAction}
      appState={appState}
      locationState={location.state}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以这样使用历史对象:

```
const ThumbsUp = ({ appState, handleEmojiAction, location, history }) => {
  if (appState === DO_SOMETHING_OVER) {
history.push("/quiet");
  }
  return (
    <EmojiLand
      EmojiBg="linear-gradient(-225deg, #DFFFCD 0%, #90F9C4 48%, #39F3BB 100%)"
      EmojiImg={thumbsUpImg}
      EmojiBtnText="You rock. Thumbs up!"
      HandleEmojiAction={handleEmojiAction}
      appState={appState}
      locationState={location.state}
    />
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，结果是一样的:

<figure>[![](img/79978be1458402fe3b1eaa8b768527c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SSogwNga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApaoJyMt2u4F3dfvYXeTdPQ.gif) 

<figcaption>使用历史道具一样好用！</figcaption>

</figure>

正如所料，我们仍然有可能重定向！

需要注意的是，您还可以像这样传入一些状态值:

```
history.push("/quiet", {
 hello: "state value"
}) 
```

Enter fullscreen mode Exit fullscreen mode

只需将第二个对象参数传入 history.push 函数。

### 我们已经开箱即用了

你有没有意识到我们不需要做任何多余的工作？

我们所要做的就是学习 react-router 提供的 API。这很好，它解释了 react-router 和 redux 开箱即可正常工作的事实。

这个应用程序使用 redux，但这不是问题。

明白了吗？

### 我们的方法有问题

实际上，到目前为止，我们讨论的方法没有任何问题。他们工作得很好！

然而，有一些警告，取决于你喜欢工作的方式，或者你正在做的项目，你可能会觉得它们难以忍受。

请注意，我在大型项目中使用过以前的模式，它们工作得很好。

然而，许多 Redux *纯粹主义者*更喜欢能够通过调度动作来导航路线。因为这是引发状态变化的主要方式。

此外，许多人也喜欢将路线数据与 Redux 存储同步，即，将路线数据保存在 Redux 存储中。

最后，他们也渴望能够在他们的 Redux devtools 中享受对时间旅行调试的支持，因为您可以在不同的路线上导航。

现在，如果 react-router 和 redux 之间没有某种更深层次的集成，所有这些都是不可能的。

那么，如何做到这一点呢？

### 考虑 React-Router 和 Redux 之间更深层次的整合

在过去，react-router 为此提供了库， [react-router-redux](https://github.com/reacttraining/react-router/tree/master/packages/react-router-redux) 。然而，在撰写本文时，该项目已被否决，不再维护。

<figure>[![](img/4dab89adafc30ac500535380b23c7ba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c1SszdzG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqRThtmohC5_iNek6ISM9zA.png) 

<figcaption>项目弃用，详见[react-router-redux github repo](https://github.com/reacttraining/react-router/tree/master/packages/react-router-redux)。</figcaption>

</figure>

我猜它仍然可以被使用，但是在生产中使用一个废弃的库你可能会有些担心。

仍然有好消息，因为 react-router-redux 的维护者建议您使用库 connected-react-router

它确实有一些设置要使用，但是如果你需要它提供的好处，这并不多。

让我们看看这是如何工作的，以及我们可以从整合到我们的项目中得到什么。

### 将 Connected-React-Router 集成到 EmojiLand 中

首先要做的是 Redux 商店。

**1。创建一个历史对象**

从技术上讲，有一个 DOM history 对象用于操纵浏览器的历史会话。

让我们以编程方式自己创建一个。

为此，从历史中导入 createBrowserHistory

在 store/index.js:

```
...
import { createBrowserHistory } from 'history' 
... 
```

Enter fullscreen mode Exit fullscreen mode

历史是 react-router-dom 包的一个依赖项，当您在应用程序中使用 react-router 时，它可能已经安装好了。

导入 createBrowserHistory 后，创建如下的历史对象:

```
..
const history = createBrowserHistory() 
```

Enter fullscreen mode Exit fullscreen mode

仍然在 store/index.js 文件中。

在此之前，商店的创建非常简单，就像这样:

```
const store = createStore(reducer); 
```

Enter fullscreen mode Exit fullscreen mode

其中 reducer 引用 reducers/index.js 中的一个 reducer 函数，但这种情况不会很快出现。

**2。缠绕根部减速器**

从 connected-react-router 库中导入以下辅助函数

```
import { connectRouter } from 'connected-react-router' 
```

Enter fullscreen mode Exit fullscreen mode

现在必须如下图所示包裹根变径管:

```
const store = createStore(connectRouter(history)(reducer)); 
```

Enter fullscreen mode Exit fullscreen mode

现在，缩减器将跟踪路由器的状态。别担心，你马上就会明白这意味着什么。

为了查看到目前为止我们所做的效果，在 index.js 中，我已经全局导出了 redux 存储，如下所示:

```
window.store = store; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在浏览器控制台中，您可以使用 store.getState()检查 redux 状态对象中的内容

这就是实际情况:

<figure>[![](img/a04e3dc1f3a80404a066e6fd5820a9e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yhGOPrag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN3A-toE_cdLRRtwxlzzN5w.gif) 

<figcaption>在开发控制台中查找现在处于冗余状态</figcaption>

</figure>

的`router`字段

如您所见，redux 商店中现在有一个路由器字段！该路由器字段将始终通过位置对象保存关于当前路由的信息，例如路径名、状态等。

我们还没完。

为了分派路由操作，我们需要应用 connected-react-router 库中的定制中间件。

这将在下面解释

**3。包括一个定制中间件**

要包含用于处理调度动作的定制中间件，从库中导入所需的 routerMiddleware 中间件:

```
...
import { connectRouter, routerMiddleware } from 'connected-react-router' 
```

Enter fullscreen mode Exit fullscreen mode

然后使用 redux:
中的 applyMiddleware 函数

```
... 
import { createStore, applyMiddleware } from "redux";
... 
```

Enter fullscreen mode Exit fullscreen mode

```
const store = createStore(
  connectRouter(history)(reducer),
applyMiddleware(routerMiddleware(history))
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们差不多完成了。只差一步了。

**4。使用连接的路由器！**

记住，react-redux 为我们提供了一个路由组件。但是，我们需要将这些路由组件包装在 connected-react-router 库中的 ConnectedRouter 组件中。

方法如下:

首先，在 index.js 中导入 ConnectedRouter 组件。

```
import { ConnectedRouter } from 'connected-react-router' 
... 
```

Enter fullscreen mode Exit fullscreen mode

下面是 index.js 文件的渲染函数:

```
render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

请记住，应用程序在应用程序中渲染不同的路线。

```
const App = () => (
  <Router>
    <Switch>
      <Route exact path="/" component={AngryDude} />
      <Route path="/quiet" component={KeepQuiet} />
      <Route path="/smile" component={SmileLady} />
      <Route path="/think" component={ThinkHard} />
      <Route path="/thumbs" component={ThumbsUp} />
      <Route path="/excited" component={BeExcited} />
    </Switch>
  </Router>
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 index.js 中，用 ConnectedRouter 组件包装 App 组件。ConnectedRouter 组件应该仅次于 react-router 中的 Provider 组件

我的意思是:

```
render(
  <Provider store={store}>
 <ConnectedRouter>
      <App />
</ConnectedRouter>
  </Provider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

还有一件事！

现在，该应用程序不会像预期的那样工作，因为 ConnectedRouter 需要一个历史属性，即我们之前创建的历史对象。

<figure>[![](img/109097ce9fea10ae6c41df2f56c6a7cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ueox7Zun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQmvirFH074vD-3ByXIBGbw.png)

<figcaption>app 现在抛出这个错误:(</figcaption>

</figure>

因为我们在多个地方需要同一个对象，所以我们需要它作为一个导出模块。

一个快速的解决方法是创建一个新的文件 store/history.js

```
import { createBrowserHistory } from "history";
const history = createBrowserHistory(); 
```

Enter fullscreen mode Exit fullscreen mode

```
export default history; 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个导出的历史对象将在两个需要它的地方使用。

在 index.js 中是这样导入的:

```
import history from "./store/history"; 
```

Enter fullscreen mode Exit fullscreen mode

然后传递给如下所示的 ConnectedRouter 组件:

```
render(
  <Provider store={store}>
    <ConnectedRouter history={history}>
      <App />
    </ConnectedRouter>
  </Provider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

这样，设置就完成了，应用程序运行了——没有我们之前看到的讨厌的错误！

请记住，我只设置了 connected-react-router，但是我鼓励您查看这个库的更高级的用法。

你可以用 connected-react-router 库做更多的事情，其中大部分都记录在官方 FAQ 的[中。此外，如果您有一个更健壮的 Redux devtools 设置和一个日志记录中间件，一定要利用时间旅行和动作日志记录！](https://github.com/supasate/connected-react-router/blob/master/FAQ.md#how-to-navigate-with-redux-action)

### 结论

我希望这和我一样有趣！

如果你有任何问题，一定要在评论区提出来，我很乐意帮忙。

去造些牛逼的东西，我一会儿来找你！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[用 React-router 和 Redux](https://blog.logrocket.com/conquer-navigation-state-with-react-router-and-redux-f1beb9b8ea7c/) 征服导航状态最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。