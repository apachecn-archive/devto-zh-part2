# 角度 Ngrx 减速器中的 Object.assign()与 Object Spread { … }🥊

> 原文：<https://dev.to/tomastrajan/objectassign-vs-object-spread----in-angular-ngrx-reducers-l2j>

# 如何在 Angular Ngrx reducers 或任何其他类似 Redux 的库中使用 Typescript 获得更好的类型检查和代码完成支持

> 本文中的所有代码示例都取自真实世界 Angular Ngrx Material Starter 项目的不同开发阶段

## Ngrx 状态库的简短概括

Ngrx 是一个状态管理库，用于实现带有集中状态存储的单向数据流。组件和服务调度动作以响应由用户交互和服务器通信触发的事件。

每个调度的动作通过触发相应的 reducer 动作处理程序产生一个新的应用程序状态。在处理程序中，我们总是希望返回新的对象或数组，以确保新的应用程序状态与旧的状态明显不同。

[阅读更多...](https://medium.com/@tomastrajan/object-assign-vs-object-spread-in-angular-ngrx-reducers-3d62ecb4a4b0)