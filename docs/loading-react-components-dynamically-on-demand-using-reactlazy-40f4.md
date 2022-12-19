# 使用 React.lazy 按需动态加载 React 组件

> 原文：<https://dev.to/dance2die/loading-react-components-dynamically-on-demand-using-reactlazy-40f4>

*照片由[霍尔格林克](https://unsplash.com/photos/K7yZ-CsDvRU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/lazy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*

React v16.6.0 引入了 [React.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) 进行代码拆分。

上一篇文章，[按需动态加载 React 组件](https://dev.to/dance2die/loading-react-components-dynamically-on-demand-gf9-temp-slug-2212651)展示了如何使用`import()`动态加载组件并启用代码拆分。

这是一篇更新的帖子，展示了如何使用`React.lazy`动态加载组件，它包装了`import()`并检索一个默认组件。

## 🗒注

我将跳过这个条目中的问题陈述，以保持简短。

| 问题陈述 |
| --- |
| [案例 1 -动态加载 React 组件](https://www.slightedgecoder.com/2017/12/03/loading-react-components-dynamically-demand/#case1) |
| [案例 2–处理不同的数据类型](https://www.slightedgecoder.com/2017/12/03/loading-react-components-dynamically-demand/#case2) |
| [情况 3–按需加载组件](https://www.slightedgecoder.com/2017/12/03/loading-react-components-dynamically-demand/#case3) |

## 🚀案例 1——动态加载 React 组件

*您可以跟随进入*[*code sandbox*](https://codesandbox.io/s/k5m609qn3o)*&查看 devtools 中* [*部署站点*](https://reactlazy1.netlify.com/) *进行编码拆分。*

在之前的版本中，我已经将组件加载到了`App.js`中的`componentDidMount`中，并将组件存储在一个名为`components`的状态中。