# 介绍反应-滚动至 v2

> 原文：<https://dev.to/ganderzz/introducing-react-scroll-to-v2-5gm8>

我维护的 React 组件的最新版本刚刚发布，react-scroll-to 的 v2。首先，它开始是我需要的一个组件，但是随着时间的推移，一些人开始使用它。因为它最初是为我的特定用例创建的，所以当需要新特性时，它开始显露出它的缺点。v2 的目标分为两部分:引入一个更灵活的 API 并实现一些高要求的特性(通过 ref 滚动和平滑滚动)。

那么什么是反应滚动呢？React-scroll-to 是一个非常简单的组件。它允许控制将浏览器窗口或组件(溢出)滚动到特定点。

我们将看几个例子来展示 react-scroll-to 可以做什么。
*(注意，在这篇文章中我将只展示渲染道具，但是 react-scroll-to 也支持高阶组件。例子可以在:【https://github.com/ganderzz/react-scroll-to】T2)*找到

### 基础知识

```
import React, { Component } from "react";
import { ScrollTo } from "react-scroll-to";

export default class MyComponent extends Component {
  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <a onClick={() => scrollTo({ y: 500 })}>Scroll to Bottom</a>
        )}
      </ScrollTo>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的组件中，我们从 react-scroll-to 导入了`ScrollTo`组件。这是一个渲染道具，所以我们将传递一个函数作为它的子对象。`ScrollTo`的 children 有一个对象作为它的参数——这是为了让这个组件的消费者能够准确地选择他们所关心的属性。通常我们只需要`scrollTo`。

`scrollTo()`是一个函数，当被调用时，将:

*   根据给定的参数滚动浏览器的窗口(在本例中，从页面顶部滚动 500 像素)
*   将`<ScrollTo />`内的所有`<ScrollArea>`组件滚动到给定的参数(我们稍后会看到)

*注意:react-scroll-to 使用绝对位置-使用相对定位的能力即将推出！*

在这个例子中，当用户点击链接时，我们会看到窗口向下滚动 500 像素！

### 滚动区

`<ScrollArea>`组件给了`scrollTo`滚动组件的能力，而不是窗口。

```
import React, { Component } from "react";
import { ScrollTo, ScrollArea } from "react-scroll-to";

export default class MyComponent extends Component {
  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <>
            <a onClick={() => scrollTo({ y: 500 })}>Scroll to Bottom</a>

            <ScrollArea>
               My long content...
            </ScrollArea>
          </>
        )}
      </ScrollTo>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为一个`<ScrollArea/>`组件作为`<ScrollTo />`的子组件存在，每当我们调用`scrollTo()`时，它将滚动所有的`<ScrollArea />`组件。因此，在这个例子中，当链接被点击时，只有“我的长内容...”区域将滚动。

但是如果我们有多个`<ScrollArea />`组件呢？

```
import React, { Component } from "react";
import { ScrollTo, ScrollArea } from "react-scroll-to";

export default class MyComponent extends Component {
  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <>
            <a onClick={() => scrollTo({ y: 500 })}>Scroll to Bottom</a>

            <ScrollArea>
               My long content...
            </ScrollArea>

            <ScrollArea>
               My long content 2...
            </ScrollArea>
          </>
        )}
      </ScrollTo>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在一个`<ScrollTo />`中存在多个`<ScrollArea />`组件的情况下，两个组件都将滚动。

如果我们只想滚动`<ScrollArea />`组件中的一个呢？

```
import React, { Component } from "react";
import { ScrollTo, ScrollArea } from "react-scroll-to";

export default class MyComponent extends Component {
  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <>
            <a onClick={() => scrollTo({ id: "content-two", y: 500 })}>Scroll to Bottom</a>

            <ScrollArea>
               My long content...
            </ScrollArea>

            <ScrollArea id="content-two">
               My long content 2...
            </ScrollArea>
          </>
        )}
      </ScrollTo>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过给一个`<ScrollArea />`添加一个`id`属性，我们能够在我们的`scrollTo()`函数中引用那个组件。`scrollTo()`然后获取另一个字段‘id’，它将滚动任何匹配该 id 的`<ScrollArea />`组件(id 必须是唯一的)。

### V2 特色

scrollTo 和 ScrollArea 在 v1 中都已经存在；让我们看看分支机构的新功能。

##### 平滑滚动

平滑滚动再简单不过了。通过向`scrollTo()`对象添加一个`smooth: true`属性，react-scroll-to 将自动应用平滑滚动。

```
import React, { Component } from "react";
import { ScrollTo } from "react-scroll-to";

export default class MyComponent extends Component {
  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <a onClick={() => scrollTo({ x: 200, smooth: true })}>
             {/* Oh yeah, we can scroll by x also! */}
             Scroll to Bottom
          </a>
        )}
      </ScrollTo>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 按 Ref 滚动

也许不是使用一个`<ScrollArea />`组件，我们有一个对我们想要滚动的组件的引用。或者，我们可能想在`<ScrollTo />`的子组件之外滚动一个组件。现在我们有能力通过`scrollTo()`参数对象提供引用了！

```
import React, { Component } from "react";
import { ScrollTo } from "react-scroll-to";

export default class MyComponent extends Component {
  myRef = React.createRef();

  render() {
    return (
      <ScrollTo>
        {({ scrollTo }) => (
          <a onClick={() => scrollTo({ ref: this.myRef, y: 200, smooth: true })}>
             Scroll to Bottom
          </a>
        )}
      </ScrollTo>

      <div ref={this.myRef}>
        I'm outside ScrollTo!
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

react-scroll-to 的 V2 提供了许多幕后的结构变化，在实现新功能的同时，允许更好的开发人员体验。我们还增加了许多新的功能，这些功能是人们所需要的。随着事物的固化，我们希望在 react-scroll-to 中暴露钩子，以允许更大的灵活性。

来看看:[反应-滚动-到](https://github.com/ganderzz/react-scroll-to)
举例:[点击这里](https://ganderzz.github.io/react-scroll-to/)

还发布在:[dylanpaulus.com](https://www.dylanpaulus.com/react/2018/11/03/introducing-react-scroll-to-v2/)