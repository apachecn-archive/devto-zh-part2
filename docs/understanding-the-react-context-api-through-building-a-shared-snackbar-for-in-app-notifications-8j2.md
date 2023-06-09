# 学习 React Context API，并将其应用到您的应用中

> 原文：<https://dev.to/sreisner/understanding-the-react-context-api-through-building-a-shared-snackbar-for-in-app-notifications-8j2>

[![](img/3e067ee51f5f02b93d5df661feaf1f19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GPqrqLWk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/512/1%2AtDxVH_WUqvu7fiNorh_EfQ.png)

### 前言

大多数应用程序需要一种方式，在通知发生时不引人注目地显示给用户。假设你正在进行八折销售，你想让你的用户一登录就知道，或者在他们提交反馈后，你想显示一条感谢信息。

Material UI 提供了一个 snackbar 组件，它非常适合这些类型的消息，所以我将在这个例子中使用它。也就是说，本文更多的是关于上下文 API，而不是材料 UI，用这种方法将材料 UI 换成任何其他组件库都非常简单。

许多应用程序需要触发来自几十个不同组件的消息。React 上下文 API 使得向所有组件提供对共享 snackbar 的访问变得非常简单，因此它们可以触发这些消息，而无需为每个消息实现单独的组件。以下是方法。

### 我们在建造什么

[![](img/a85cca6e38df0b8d5296040f43b41ebf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r8xDldGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/364/1%2Ad8iHKTMYovt37-tA1pF4mQ.gif)

### 设置/依赖关系

这篇文章假设你已经有了一个 React 应用，并安装了 **@material-ui/core 1.0.0+** 和 **@material-ui/icons 1.0.0+** 作为依赖项。

### 创建上下文

首先，我们需要创建我们的上下文 API **提供者**和**消费者**组件。**提供者** *向所有**消费者**提供*我们的 snackbar 的状态，以及操纵该状态的一些函数。这允许所有子组件访问和操作**提供者**的状态，不管它们在组件层次结构中有多深。不需要正确钻孔！