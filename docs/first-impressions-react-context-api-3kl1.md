# 第一印象:反应上下文 API

> 原文：<https://dev.to/thejohnstew/first-impressions-react-context-api-3kl1>

*亚历山大·奈特在 Unsplash 上的照片*

大约一个月前，React 推出了新的上下文 API[。在过去的几天里，我一直在玩它，并对它的工作原理有了一些了解。它引入了一些需要考虑的新用例，并对一些旧用例有所帮助。在这篇文章中，我深入思考了这个问题，以及我认为新的上下文 API 是如何让我们的开发者生活变得更好的。](https://github.com/facebook/react/releases/tag/v16.3.0)

## 新 API

新的[上下文 API](https://reactjs.org/docs/context.html#api) 由以下三部分组成:

*   [反应。createContext](https://reactjs.org/docs/context.html#reactcreatecontext)
*   [供应商](https://reactjs.org/docs/context.html#provider)
*   [消费者](https://reactjs.org/docs/context.html#consumer)

利用以上三块我们现在真的可以做很多。让我们快速看一下如何使用它们。

```
import React from 'react';
import { render } from 'react-dom';

const theme = {
  buttonColor: 'blue'
};

const ThemeContext = React.createContext(theme);

const AppButton = () => (
  <ThemeContext.Consumer>
    {({ buttonColor }) => (
      <button style={{ background: buttonColor }}>App Button</button>
    )}
  </ThemeContext.Consumer> );

const Container = () => (
  <div>
    <AppButton />
  </div> );

const App = () => (
  <div>
    <h1>My App</h1>
    <ThemeContext.Provider value={theme}>
      <Container />
    </ThemeContext.Provider>
  </div> );

render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

[![Edit 387l8656xp](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/387l8656xp)

上面是一个创建一些上下文并使用它的基本例子。第 8 行我为应用程序主题创建了一些上下文。创建上下文给了我两个新的 React 组件`{ Provider, Consumer }`。在第 27 行，我使用了`Provider`来允许子组件访问我们创建的主题上下文。然后在我的`AppButton`组件的第 11 行，我使用上下文的`Consumer`部分来访问主题信息。这个例子很小，但是说明了使用新的上下文 API 背后的基本设置。

## 熟悉度

如果你曾经建立过一个 [React Redux](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#provider-store) 项目，那么 API 的`Provider`部分应该看起来非常熟悉。我不确定这两个代码库的内部是如何工作的，但是很明显 React 和 [Redux](https://redux.js.org/) 的意图是一样的。

它们的不同之处在于都希望子组件使用状态。Redux 在状态如何通过 reducers 和 actions 更新的问题上更固执己见，但是 React 允许你以你认为合适的方式读取和更新状态。

更进一步，Redux 公开了一个更高阶的组件来连接到商店，但是在 React 中，它们通过一个子函数来公开状态。两者都达到了相同的目标，但是 Redux 更具规范性。

如果您以前曾经处理或设置过状态管理库，那么您应该对这个 API 的大致内容比较熟悉。

## 滚自己的状态管理

有了上下文 API，您现在可以更轻松地管理小型应用程序状态。如果你有一个小的单页应用程序，只有你或一个小团队在工作，那么我认为使用上下文 API 应该足以管理你所有的应用程序状态。

也就是说，你应该想出一个你理解的模式，或者从 Redux 或其他状态库中提取概念，并在你的规模上尝试它们。

最终，它是关于什么让你舒服，什么对应用程序有用。如果 Redux 是你的面包和黄油，那就去做吧。如果您刚刚开始使用 React，并且发现自己不知道如何在这里和那里传递状态，那么现在就查看一下上下文 API。

如果在某些时候你觉得还不够，谷歌“反应状态管理库”并尝试一些。

## 我喜欢 Redux

我使用 Redux 已经有一段时间了，我喜欢它的工作方式。我非常喜欢 Redux，以至于我把它用在了根本不使用 React 的项目上。Redux 是一个很棒的状态管理库，它做了我需要它做的事情。我唯一不喜欢 Redux 的地方是我会为 actions 和 reducers 创建过多的文件。我用[鸭子](https://github.com/erikras/ducks-modular-redux)解决了这个问题。这是 Redux 的一个很好的模式，也是我的首选方法。看看吧！

上下文 API 将使我现在很少使用 Redux。在一个团队环境中或者在一个中型到大型的项目中，我仍然会使用 Redux。在一天结束的时候，你需要做的是让你和你的团队感到舒适，并最终让你的生活更轻松。

## 最后的想法

总之，我喜欢新的上下文 API，并认为它提供了大量的价值。对于那些刚刚开始使用 React 的人来说，它应该能够更快地回答全局应用程序状态问题，而不是向下传递 props 并学习状态管理库。

查看下面的一些链接，了解关于上下文 API 的更多信息:

*   [反应上下文文档](https://reactjs.org/docs/context.html)
*   Kent C. Dodds 的新上下文 API

如果你喜欢这篇文章，那么请展示一些❤️.如果你没有，那我们就来谈谈😃。

最初发布在我的博客上，地址为 [johnstewart.io](https://goo.gl/U7Lyby) 。