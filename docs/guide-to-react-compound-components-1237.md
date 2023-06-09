# 化合物成分反应指南

> 原文：<https://dev.to/bnevilleoneill/guide-to-react-compound-components-1237>

### 快速引导化合物成分发生反应

[![](img/cd7ef13636286dd570de5a5c4edf822c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HWMd-sT2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/391/1%2A3SVfBkNZI2f-sspiq59xcw.png)

复合组件是一种高级模式，所以它可能难以使用。本指南旨在帮助您理解该模式，以便您能够自信而清晰地有效使用它。

注意——在本文中，我们将使用 React v16.3 中引入的新的[上下文 API](https://reactjs.org/docs/context.html) 。其他[文章](https://dev.to/shaunoff/using-advanced-design-patterns-to-create-flexible-and-reusable-react-components-part-1-2040-temp-slug-5672359)使用 React.cloneElement 实现该模式，但这涉及到克隆每个组件，这会增加内存占用。它还将组件限制为父组件的直接子组件。所以使用上下文 API 要好得多。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 什么是复合成分？

复合组件是一种模式，在这种模式中，组件一起使用，这样它们共享一个隐式状态，让它们在后台相互通信。

> 想想像 HTML 中的和元素这样的复合组件。分开时，它们不会做太多，但合在一起时，它们能让你创造完整的体验。肯特·c·多兹

当您单击一个选项时，select 知道您单击了哪个选项。像 select 和 option 一样，组件自己共享状态，您不必显式地配置它们。

### 复合构件的例子

```
<TabSwitcher>
  <header>
    <ul className="tablist">
      <li>
        <Tab id="a">
          <button>Tab A</button>
        </Tab>
      </li>
      <li>
        <Tab id="b">
          <button>Tab B</button>
        </Tab>
      </li>
    </ul>
  </header>
  <main>
    <TabPanel whenActive="a">
      <div>
        a panel
      </div>
    </TabPanel>

    <TabPanel whenActive="b">
      <div>
        b panel
      </div>
    </TabPanel>
  </main>
</TabSwitcher> 
```

当您单击选项卡组件内的按钮时，会呈现相应的选项卡面板内容。

### 我有渲染道具为什么还需要？

渲染道具是一个很好的模式。它非常通用，也很容易理解。然而，这并不意味着我们必须在任何地方都使用它。如果不小心使用，它会导致混乱的代码。
[![](img/74a8ef8c3335be13cb8c3192b25f8cb5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--i7Q5uLRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1rtpaz3pkwde1rpznl67.png)

[![](img/1ca9635298918e913f681e7c64836c8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O87Re1FS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7iyq3bj253tc9bz9ceg4.png)

在标记中有太多的嵌套函数会导致阅读困难。记住**没有什么是银弹**，连渲染道具都没有。

### 复合组件模式的优点

通过看这个例子，一些优势是非常明显的。

*   开发者拥有标记。标签切换器的实现不需要固定的标记结构。你可以做任何你想做的事情，嵌套一个 10 层深的标签(我不是在评判)，它仍然可以工作。
*   开发人员可以按任何顺序重新排列组件。假设您希望选项卡位于选项卡面板的下方。组件实现中不需要任何改变，我们只需要在标记中重新排列组件。
*   这些组件不必显式地挤在一起，它们可以独立编写，但仍然能够通信。在示例中，Tab 和 TabPanel 组件没有直接连接，但是它们能够通过它们的父 TabSwitcher 组件进行通信。

### 工作原理

*   父组件(TabSwitcher)有一些状态。
*   使用 context-api，TabSwitcher 与子组件共享它的状态和方法。
*   子组件选项卡使用共享方法与 TabSwitcher 通信。
*   子组件 TabPanel 使用共享状态来决定是否应该呈现其内容。

### 实现 TabSwitcher 复合组件

要实现一个组件，我通常遵循以下步骤。

1.  列出所需的组件。
2.  写样板文件。
3.  实现单个组件。

#### 列出需要的组件

对于 TabSwitcher，我们需要两件东西。一是知道显示哪个选项卡内容，二是当用户单击时切换选项卡面板。

这意味着我们需要控制选项卡面板内容的呈现，并在选项卡上有一个单击事件侦听器，因此当单击选项卡时，会显示相应的选项卡面板内容。

为此，我们需要三个组件。

1.  TabSwitcher —保存状态的父组件
2.  选项卡—告知其父组件是否被单击的组件
3.  tab panel——当父组件告诉它时呈现的组件

#### 写样板文件

复合组件模式有一些样板代码。这很好，因为在大多数情况下，我们可以不用太多思考就能写出来。

```
import React, { Component, createContext } from 'react';

const context = createContext({});

const { Provider, Consumer } = context;

const Tab = ({ id, children }) => (
  <Consumer>
    {({ changeTab }) => <div onClick={() => changeTab(id)}>{children}</div>}
  </Consumer>
);

const TabPanel = ({ whenActive, children }) => (
  <Consumer>
    {({ activeTabId }) => (activeTabId === whenActive ? children : null)}
  </Consumer>
);

class TabSwitcher extends Component {
  state = {
    activeTabId: 'a'
  };

  changeTab = newTabId => {
    console.log(newTabId);
    this.setState({
      activeTabId: newTabId
    });
  };

  render() {
    return (
      <Provider
        value={{
          activeTabId: this.state.activeTabId,
          changeTab: this.changeTab
        }}
      >
        {this.props.children}
      </Provider>
    );
  }
}

export default TabSwitcher;
export { Tab, TabPanel }; 
```

我们在这里创造一个环境。子组件将从上下文中获取数据和方法。数据将是由父共享的状态，方法将用于将状态的改变传送回父。

#### 实现单个组件

**标签组件**需要监听点击事件，并告诉 parent 哪个标签被点击了。可以这样实现——

```
const Tab = ({ id, children }) => (
  <Consumer>
    {({ changeTab }) => changeTab(id)}>{children}}
  </Consumer>
); 
```

选项卡组件采用 id 属性并在点击事件时调用传递其 id 的 **changeTab** 方法。这样，父节点就知道哪个标签被点击了。

**TabPanel 组件**只有当它是活动面板时才需要呈现其子面板。可以这样实现——

```
const TabPanel = ({ whenActive, children }) => (
  <Consumer>
    {({ activeTabId }) => (activeTabId === whenActive ? children : null)}
  </Consumer>
); 
```

TabPanel 组件使用 **whenActive prop** 告诉它何时渲染子组件。上下文提供了 **activeTabId** ，通过它 TabPanel 决定是否应该呈现其子元素。

**TabSwitcher 组件**需要维护活动选项卡状态，并将状态和方法传递给子组件。

```
class TabSwitcher extends Component {
  state = {
    activeTabId: 'a'
  };

  changeTab = newTabId => {
    this.setState({
      activeTabId: newTabId
    });
  };

  render() {
    return (
      <Provider
        value={{
          activeTabId: this.state.activeTabId,
          changeTab: this.changeTab
        }}
      >
        {this.props.children}
      </Provider>
    );
  }
} 
```

TabSwitcher 组件存储 **activeTabId** ，默认为‘a’。因此第一个面板最初是可见的。它有一个 **changeTab 方法**，用于更新 activeTabId 状态。TabSwitcher 向消费者共享状态和方法。

让我们看看它们是如何组合在一起的。

```
import React, { Component, createContext } from 'react';

const context = createContext({
  activeTabId: '',
  changeTab: () => {}
});

const { Provider, Consumer } = context;

const Tab = ({ id, children }) => (
  <Consumer>
    {({ changeTab }) =>  changeTab(id)}>{children}}
  </Consumer>
);

const TabPanel = ({ whenActive, children }) => (
  <Consumer>
    {({ activeTabId }) => (activeTabId === whenActive ? children : null)}
  </Consumer>
);

class TabSwitcher extends Component {
  state = {
    activeTabId: 'a'
  };

  changeTab = newTabId => {
    this.setState({
      activeTabId: newTabId
    });
  };

  render() {
    return (
      <Provider
        value={{
          activeTabId: this.state.activeTabId,
          changeTab: this.changeTab
        }}
      >
        {this.props.children}
      </Provider>
    );
  }
}

export default TabSwitcher;
export { Tab, TabPanel }; 
```

复合组件可以这样使用:

```
import React from "react";
import ReactDOM from "react-dom";

import TabSwitcher, { Tab, TabPanel } from './TabSwitcher';

function App() {
  return (
    <div className="App">
      <h1>TabSwitcher with Compound Components</h1>
      <TabSwitcher>
        <Tab id="a">
          <button>a</button>
        </Tab>
        <Tab id="b">
          <button>b</button>
        </Tab>

        <TabPanel whenActive="a">
          <div>
            a panel
          </div>
        </TabPanel>

        <TabPanel whenActive="b">
          <div>
            b panel
          </div>
        </TabPanel>
      </TabSwitcher>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

演示(当心这不是 CSS 的地盘)
[https://codesandbox.io/embed/5v253zqk4p](https://codesandbox.io/embed/5v253zqk4p)

如果你觉得这篇文章有帮助，请为它鼓掌。在 [Twitter](https://twitter.com/dev__adi) 和 [Medium](https://medium.com/@adityaa803/) 上关注我，当我发布更多类似的文章时会收到通知。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[反应化合物成分快速指南](https://blog.logrocket.com/guide-to-react-compound-components-9c4b3eb482e9/)首先出现在[博客](https://blog.logrocket.com)上。