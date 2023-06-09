# ProppyJS:组件的功能性 props 组合(支持 ReactJS 和 VueJS)

> 原文：<https://dev.to/fahad19/proppyjs-functional-props-composition-for-components-supports-reactjs--vuejs-198k>

[![ProppyJS](img/c0fb36f83e7f67ea5ad2cab7b66c710c.png)T2】](https://proppyjs.com)

ProppyJS 是一个很小的 JavaScript 库，用于编写道具(组件接收来呈现自身的对象)。

生成的道具可以用在你最喜欢的基于组件的 UI 框架中(比如 React 或 Vue.js)。

## 概述

*   将逻辑提升到组件之上一个级别
*   允许在组件树中的任何位置访问应用程序范围的依赖项(如 Redux store、config 等)
*   逻辑层和表示层的清晰分离
*   您的组件变得无状态
*   更易于单元测试
*   连接到任何 UI 组件库(支持 React、Preact 和 Vue.js)
*   与其他库的互操作性(Redux 和 RxJS 集成)
*   分散在不同封装中的小尺寸，只专注于一件事

## 链接

*   文献:[https://proppyjs.com/docs/introduction](https://proppyjs.com/docs/introduction)
*   https://proppyjs.com/docs/api
*   快速入门:[https://proppyjs.com/docs/quickstart](https://proppyjs.com/docs/quickstart)
*   例子:[https://proppyjs.com/docs/examples/react-counter/](https://proppyjs.com/docs/examples/react-counter/)

## 套餐

| 包裹 | 状态 | 大小 | 描述 |
| --- | --- | --- | --- |
| [proppy](https://proppyjs.com/docs/packages/proppy) | [![proppy-status](img/351064a6ea648323920bade7426530a7.png)T2】](https://npmjs.com/package/proppy) | `1.5K` | 核心包 |
| [proppy-react](https://proppyjs.com/docs/packages/proppy-react) | [![proppy-react-status](img/5f251e84a4853e57f50bb264a2fbb8c5.png)T2】](https://npmjs.com/package/proppy-react) | `1.0K` | 反应积分 |
| 命题视图 | [![proppy-vue-status](img/59055972ca7348c42a9d59d2684514fd.png)T2】](https://npmjs.com/package/proppy-vue) | `0.7K` | Vue.js 集成 |
| [proppy-preact](https://proppyjs.com/docs/packages/proppy-preact) | [![proppy-preact-status](img/8ef7d7d729bd2fa796bf03d16c540b59.png)T2】](https://npmjs.com/package/proppy-preact) | `1.1K` | 预先整合 |
| [proppy-redux](https://proppyjs.com/docs/packages/proppy-redux) | [![proppy-redux-status](img/ee8c022c05c6d2b436b8f524e9985171.png)T2】](https://npmjs.com/package/proppy-redux) | `0.6K` | 冗余集成 |
| [proppy-rx](https://proppyjs.com/docs/packages/proppy-rx) | [![proppy-rx-status](img/900d707981b0cb3ca6c18e2e260899e1.png)T2】](https://npmjs.com/package/proppy-rx) | `0.6K` | RxJS 集成 |

## 比较

### Redux

Redux 是基于动作和 Redux 为你的整个应用程序进行状态管理。

Proppy 旨在将逻辑提升到组件之上的一个层次，并且只处理组件的行为。

虽然`react-redux`允许您从组件树中的任何地方访问您的单个商店，但是 Proppy 允许您访问[提供者](https://proppyjs.com/docs/providers)。

例如，Redux store 可以是这里的提供者之一。

Proppy 并不局限于您在应用程序状态管理中使用的内容，也不会将您局限于任何特定的状态管理或渲染库。

### 重新构图

[重组](https://github.com/acdlite/recompose)是建造 ProppyJS 的最初灵感。

几个主要区别:

*   不依赖于任何渲染库(如 React 或 Vue.js)
*   允许访问应用程序范围的依赖项(如 Redux store、config 等)
*   仅处理道具生成
*   不会为每个函数在树中创建新组件