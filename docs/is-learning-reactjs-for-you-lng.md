# 学习对你有用吗？

> 原文：<https://dev.to/yazanaabed/is-learning-reactjs-for-you-lng>

[*ReactJS*](https://reactjs.org/) *是一个令人印象深刻的创建事物和构建用户界面的库。来自脸书的团队为我们做的。*

<figure>[![](../Images/c3b75696bc25698bc0957fe991b750f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MxC0OC4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AI2LVy4WMsn6Umd4R) 

<figcaption>照片由[菲利克斯·拉塞尔-拉锯](https://unsplash.com/@frsphoto?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

你可以在 twitter 上关注我，或者在我的网站 yaabed.com 上查看我的最新文章。另外，我在 blog.yaabed.com 媒体的[有我的出版物。](https://medium.com/yazanaabed)

在我学会反应之前，我不知道为什么人们那么喜欢用它工作，而且公司每次前端面试都会问这个问题。此外，人们告诉我，在一个单独的文件中编写 HTML 是你的项目的最佳实践，现在 React 团队在你的 JavaScript 文件中以另一种方式编写模板。

在你学会反应之前，有太多的问题浮现在你的脑海里。然而，我决定开始学习 React。在我学习了 React 之后，我明白了为什么我们要这样写模板。它向我展示了 React 是 web 开发的未来，它改变了我对开发 web 应用程序的看法。

液体错误:内部

你可以自己学会反应。推荐每个前端工程师都去学。React 扩展了你的知识，尤其是你如何看待你的设计、你的组件、你的用户界面、你的应用程序架构。

在本文中，我将概述我对 ReactJS 的了解以及我对它的了解。我希望你喜欢读这篇文章。

观看此视频，了解关于 React 的五件事。——[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1) & [伯克·h✪lland](https://medium.com/u/d4377b6d15d2)

[https://www.youtube.com/embed/3DCeoCpTIAI](https://www.youtube.com/embed/3DCeoCpTIAI)

在我开始讨论任何 React 项目的构建项目之前，我建议人们观看这些课程:

*   [初学反应指南](https://egghead.io/courses/the-beginner-s-guide-to-react)——[肯特·c·多兹](https://medium.com/u/db72389e89d8)。
*   [为初学者反应](https://reactforbeginners.com/)——[韦斯博斯](https://medium.com/u/86a55cd7983b)。
*   [完成介绍反应](https://frontendmasters.com/courses/react/)——[布莱恩·霍尔特](https://medium.com/u/69ba9847d710)。

我讨论了任何 React 应用程序的元素，以及使用 React 的任何应用程序的起点在哪里。此外，我还概述了构建任何 React 应用程序的步骤。

<figure>[![](../Images/611baa1ff13708a7cd1cf53975d56870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0z21uyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvV2mYuwX7lcZsncydIlvvw.png) 

<figcaption>这是我第一个 React 应用🔥✨</figcaption>

</figure>

*   我们需要输入反应写反应应用程序。
*   我们需要从 react-dom 导入渲染函数。
*   我们需要使用 render 方法定义在哪里呈现我们的应用程序。

### 根元素

任何 React 应用程序都需要一个根容器来启动。render 函数让我们能够将任何 React 组件渲染到 HTML DOM 中。我最喜欢这个渲染函数的一点是，您可以轻松地测试您的应用程序，并使用您的测试库将您的应用程序渲染为假的 DOM 元素。

[![](../Images/10aa2a797df840c866c71b67b65a11e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7dKCUN9h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeXmNdsutdSN2npW2PpOzXg.png)

### 组件

组件是 MVC 中的一个视图。当你看到应用程序设计时，你就划分了你的应用程序。例如标题、侧边栏、按钮。

此外，组件可以是页面或容器，如主页、登录、仪表板。

React 应用程序分为多个 React 组件，每个组件都包含一个 render 方法，它是组件的入口点。

<figure>[![](../Images/999b98fb8911c07ea9642b10ca437575.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gowGHAbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZNLMhhwo0huzxojc1-0gtg.png) 

<figcaption>段落构件</figcaption>

</figure>

React 库使用 render 方法可以知道在使用段落组件时要呈现什么。这就是我们在项目中使用组件的方式。

<figure>[![](../Images/c753f25e5139f064df33d3b096326fe3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SKy4Mmak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWufN_zS7DKCe-xHK0dMhbA.png) 

<figcaption>使用段落组件</figcaption>

</figure>

我建议你试着自己构建组件，或者试着把工作中得到的任何设计分成组件，并思考为什么要这样划分。

在将应用程序划分为组件之前，请务必理解这句话。

> "理想情况下，一个组件应该只负责一部分功能."— [Fullstack React](https://www.fullstackreact.com/) ，[索菲亚·舒梅克](https://medium.com/u/fac5e491c445) & [内特·默里](https://medium.com/u/6538666cdb34)。

> ![Programming Wisdom profile image](../Images/c0e04370043baff7812aa9d1c701cd8f.png)编程智慧[@ code Wisdom](https://dev.to/codewisdom)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)“可靠性的中心敌人是复杂性。”——格尔等人2018 年 06 月 03 日下午 16 点 29 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1003312607418347521)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1003312607418347521)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1003312607418347521)

### 组件包含两件初级武器

您可以使用这两种武器来提高组件的可重用性和灵活性。

#### 道具

Props 是组件的输入，由父组件拥有并从那里传递。任何组件使用的道具都比其他组件更具可重用性，它把自己的值隐藏在里面。

[![](../Images/b3c8999aa5d48a9490681196bf28028d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yKqFfloG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AR4ZiZ7yoeIjAvQk6Z7ygxA.png)

Props 是可重用性的主要武器，因为如果我们在 blogs 组件中定义 blogs 数组，就不能在应用程序的其他地方使用，也不能在应用程序需要的其他地方使用其他 Blogs 值。

这种类型的组件只与称为无状态组件的道具一起工作。每次给同一个组件相同的输入，它都会呈现相同的输出或相同的 DOM。

开始使用道具时要检查的事项:

*   [使用 PropTypes 进行类型检查](https://reactjs.org/docs/typechecking-with-proptypes.html)
*   [组件和道具](https://reactjs.org/docs/components-and-props.html)
*   [展示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)——[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1)

#### 状态

组件本身拥有一个状态，状态可以使用组件本身来修改，或者可以使用来自外部的 props 来初始化。

初始化状态的地方是在构造函数内部，之后您需要使用 React 中的 setState 方法。组件对象来修改组件的状态。

<figure>[![](../Images/e7e773d568531ffdc371e414eab8619f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZgl81_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ac84rUbTht2PcCLMMZwytAQ.png) 

<figcaption>登录组件包含其状态并管理其状态。</figcaption>

</figure>

如果你不明智地使用它们，这两种武器可能会成为你的敌人，主要是当你在错误的地方使用 setState 方法并在没有任何需要的情况下触发重新渲染时。所以，在设计组件和使用什么组件之前，请三思。如果组件不获取状态并从外部获取值，如果组件管理它的状态，这称为有状态组件，这称为无状态组件。

<figure>[![](../Images/71af28e8b0b7973d9b28eafe2037bb4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O-1dLK5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ab3SyKELKooLlcupz) 

<figcaption>照片由[莱昂·罗哈斯](https://unsplash.com/@ay_leon?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

### 虚拟 DOM

React 团队为我们修改东西它不允许我们使用浏览器 dom 是为我们建立的叫做虚拟 DOM 的东西。React 团队以优化的方式处理 DOM 的变化。

#### 手动更改 DOM 的问题:

*   很难跟踪每一个变化。
*   在某些情况下，使用 JavaScript 更改 DOM 会很慢。
*   脸书团队做了一个演讲，说 JavaScript 非常快，但是修改 DOM 太慢了。
*   React 团队表示，您的组件必须返回您希望看到的 HTML，react 团队将承担起为您更新视图的责任。

React 团队不从 DOM 获取状态的主要原因是因为 DOM 太慢了，所以他们决定从他们这边改变状态。

> 为什么我们要从组件的 DOM 表示中获取组件的状态呢？有两个很好的理由:
> 
> 1)更快，2)更简单。— [REACT、REDUX 和 JAVASCRIPT 架构](https://jrsinclair.com/articles/2018/react-redux-javascript-architecture/)

[https://www.youtube.com/embed/NVWrdSz2Q5c](https://www.youtube.com/embed/NVWrdSz2Q5c)

### 构建 react 项目的步骤

React 应用程序的第一件事是将你的 UI 分成组件，并确保每个组件做一件事，负责一件事。

当您构建应用程序组件时，尝试使用静态值构建组件，并确保它在没有任何服务器或 BE 的情况下也能正常工作。

想想你的应用程序的哪个组件包含一个状态。为组件添加静态状态，并尝试更改它。添加事件触发器以在组件中移动数据。之后，将服务器添加到您的组件中。

### React 组件的时间轴

你可以在这里阅读更多关于 React 生命周期的信息。

*   组件的构造函数运行了，初始状态也运行了。
*   然后调用 render 方法，然后调用 render for children 以确保呈现组件。
*   初始渲染现在运行。
*   componentDidMount 生命周期被触发。

### 路由与反应

用户请求 index.html 页面，然后服务器不关心用户做什么。

当您在浏览器上构建用户路由器时，这称为单页应用程序。

React router:当路由改变时，应用程序与路由器提供者一起包装，所有组件监听改变，并检查路径是否呈现，否则返回 null。

### 结论

最后，我建议每个前端工程师都尝试一下 ReactJS 这是一个非常棒的库，可以用来构建令人惊叹的应用程序。不要害怕这些新事物，你能做到。试着自己动手做东西。这里有一篇[文章](https://medium.com/hackteam/5-examples-of-react-applications-build-on-open-source-media-apis-c5b9880d922d)，介绍如何使用开源媒体 API 做出反应。