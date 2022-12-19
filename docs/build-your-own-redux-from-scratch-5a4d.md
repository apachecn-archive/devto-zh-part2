# 从头开始构建您自己的 redux

> 原文：<https://dev.to/sait/build-your-own-redux-from-scratch-5a4d>

Redux 是 React 应用程序的状态管理库，因为它有助于管理单个对象中的应用程序状态，这意味着整个应用程序状态存在于单个对象中。

如果你试图连接一个 redux 商店，你必须对你的 react 应用程序做一些样板设置，这经常会令人困惑。

所以这就是我们从头开始写的原因。

在 react 应用程序中创建 store.js 文件。

首先，我们需要创建一个调度函数、订阅函数、thunk 函数