# 做一只刺猬-第二部分

> 原文：<https://dev.to/booyaa/web-app-development-core-skill-hedgehog---part-two-3p1i>

*照片由[柳德米拉·德尼修](https://unsplash.com/photos/ULXu46fVCHY)在 Unsplash* 上拍摄

*这是我的“写作业”博客系列的一部分，介绍性的文章可以在[这里](https://dev.to/booyaa/blogging-my-homework-3ohf)找到。*

**9 月 22 日更新:**我通过了刺猬考核！

欢迎回到我的 Web 应用程序开发学习指南的第二部分！在[之前的](https://dev.to/booyaa/web-app-development-core-skill-hedgehog---part-one-2144)帖子中，我们讨论了评估的实践方面，这次我们讨论理论方面。

## 理解

我们将在九个方面对您的 React 知识进行评估(以问答的形式),我们将在单独的部分介绍每个方面。

作为这项核心技能的学习材料的一部分，React 的文档被认为是一个很好的信息来源，尤其是[主要概念](https://reactjs.org/docs/hello-world.html)部分。虽然不是评估的一部分，但你应该理解 JSX 和元素。

如果您像我一样有一段时间没有接触过 JavaScript，React 文档为您重新熟悉和理解一些新语法提供了极好的参考:

参考

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/A _ re-introduction _ to _ JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)
*   [https://gist . github . com/Gaea Ron/683 e 676101005 de 0 add 59 e 8 bb 345340 c](https://gist.github.com/gaearon/683e676101005de0add59e8bb345340c)

### 有用的反应知识

虽然这些不是评估的一部分，但我们希望您对它们有所了解。

#### JSX

是 Javascript 语法扩展的缩写，它产生 React“元素”。

重要提示:JSX 比 HTML 更接近 JavaScript，React DOM 使用`camelCase`属性命名约定，而不是用于 HTML 属性的命名约定。例如`tabindex`变成了`tabIndex`

JSX 的有用属性:

*   通过对呈现前嵌入的任何值进行转义来防止注入攻击
*   表示对象

参考

*   [介绍 JSX–反应](https://reactjs.org/docs/introducing-jsx.html)

#### 元素

React 元素包括:

*   是普通的对象，创建起来很便宜，不像浏览器 DOM 元素。
*   不是组件(稍后将详细介绍)
*   不可变的，即一旦创建，就不能改变它的子元素或属性。

参考

*   [渲染元素–反应](https://reactjs.org/docs/rendering-elements.html)

### 使用 React 会有好处的情况

学习技术资源提供了一个很好的起点来理解我们什么时候应该使用 React，或者实际上是 HTML 和 CSS 背后的任何类型的框架:

*   您的应用程序是否需要大量的客户端业务逻辑？
*   需要**简单可复用组件**吗？
*   你的应用能从**选择性重载**中获益吗？也就是说，页面上有什么东西需要反应吗？
*   现有的客户开发团队是否已经在使用 **React 或 Node** ？
*   你的应用程序需要大量的 DOM 操作吗？
*   客户是否会从**快速的页面加载时间**中受益匪浅？

我觉得最后一点与现代框架的构建工具有关，这些工具是为了更快的加载时间而优化页面，而不是特定于 React 的属性。

现在花点时间想想你自己对应用程序的想法，如果你对大多数问题的回答是肯定的，那么也许 React 是最好的工具。

思考什么时候**不合适**做出反应也很有用，下面是我能想到的一些例子:

*   应用程序在构建后是否保持静态，比如像 Jekyll 这样的静态站点生成器？
*   除了页面导航之外，应用程序不需要任何交互吗？
*   我们要用最新最棒的技术！
*   Javascript 无处不在，为什么不随波逐流呢！

我从 CSS-Tricks 的“优秀的[”一个项目什么时候需要反应？](https://css-tricks.com/project-need-react/)文章。

参考

*   [使用 React 开发 Web 应用程序|通过制造技术学习技术](https://learn.madetech.com/core-skills/web-application-development-with-react#sections)

### 什么是 React 组件

组件允许你将用户界面分割成独立的、可重用的部分，这些部分可以独立地工作。

如果您曾经做过窗体或 Windows 应用程序开发，您可以将控件工具箱视为组件的类似物。

React 页面也为组件提供了一个简单的类比，它们**就像 JavaScript 函数**。

约定:组件名总是以大写字母开头，这是因为 React trees 组件以小写字母开头作为 DOM 标签。

组件可以引用其他组件的输出。惯例是创建一个调用其他组件的`App`组件。对于重构来说，了解这一点很重要。

参考

*   [组件和道具–反应](https://reactjs.org/docs/components-and-props.html)

### 什么是组件内的道具

如果我们继续模拟一个应用程序的控件，那么我们可以把 props(properties 的缩写)看作完全一样的东西。您可以定义控件的属性。

React 页面提供的另一个视图是“props”是输入，它们返回一个 React 元素，描述屏幕上应该显示什么。

重要提示:道具是只读的，如果你的组件修改了它自己的道具，它就被认为是“不纯的”。在 React 中，您不能违反此规则。

参考

*   [道具是只读的–反应](https://reactjs.org/docs/components-and-props.html#props-are-read-only)

### 什么是组件内的状态

状态类似于道具，但是是私有的，完全可以由组件控制。回想一下上一节，道具是不可变的，也就是说它们是只读的。

本地状态只对 JavaScript 类可用，对 JavaScript 函数无效。

状态是私有的，完全由组件控制。

*   不要直接修改状态，总是使用`setState()`
*   状态更新可能是异步的，这是因为为了提高性能，React 可能会将多个`setState()`调用批处理到单个更新中。使用第二种形式的`setState()`并传递给它一个函数而不是一个对象。
*   合并状态更新

参考

*   [状态和生命周期–反应](https://reactjs.org/docs/state-and-lifecycle.html)

### 指什么道具

组件的所有子组件，子组件可以是:

*   一切，不一定是其他组件
*   文本
*   JavaScript 函数

React 说*孩子是一个不透明的数据结构* (Reach.children)

参考

*   [React 道具快速介绍.儿童–代码爆炸](https://codeburst.io/a-quick-intro-to-reacts-props-children-cb3d2fce4891)
*   [反应过来这个道具小朋友——Learn.co](https://learn.co/lessons/react-this-props-children)
*   React - Max Stoibers 博客中对儿童的深入探究

### React 允许你如何响应用户事件

它们的行为类似于对 DOM 元素的事件处理，只是语法不同:

*   React 事件使用大小写而不是小写来命名。
*   JSX 将函数作为事件处理程序传递，而不是字符串
*   你必须显式调用`preventDefault`而不是返回`false`。

参考

*   [处理事件–反应](https://reactjs.org/docs/handling-events.html)

### 一种设计 react 组件样式的方法

*   内嵌样式
*   镭
*   外部样式表
*   CSS 模块

参考

*   [造型和 CSS–反应](https://reactjs.org/docs/faq-styling.html)
*   [DOM 元素–反应](https://reactjs.org/docs/dom-elements.html#style)
*   [造型反应](https://survivejs.com/react/advanced-techniques/styling-react/#react-based-approaches)
*   [造型反应组件|杰克·特伦特](http://stylingreact.com/)

### 组件生命周期

生命周期挂钩用于在资源组件被销毁时释放它们。

*   使用`componentDidMount`方法设置(反应术语:安装)
*   使用`componentWillUnmount`方法拆卸

生命周期方法确保当组件被销毁时，有价值的资源被释放。通过定义`componentDidMount`(分配)和`componentWillUnmount`(释放)方法。这可能类似于具有垃圾收集器的编程语言。

参考

*   [向类添加生命周期方法](https://reactjs.org/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class)

### 一种管理应用程序状态的方法

*   Redux
*   上下文 API(反应)
*   组件状态(非常基本)
*   JS 模块单例模式
*   未说明的
*   MobX

参考

*   [状态的状态:2018 - DEV 社区中的 React 状态管理👩‍💻👨‍💻](https://dev.to/jpnelson/the-state-of-the-state-react-state-management-in-2018-2l0c)
*   [React 应用状态的 5 种类型——詹姆斯·K·尼尔森](http://jamesknelson.com/5-types-react-application-state/)
*   [应用状态管理–kentcdodds](https://blog.kentcdodds.com/application-state-management-66de608ccb24)
*   [使用 Mobx 管理 React 应用状态—全栈教程(第 1 部分)](https://levelup.gitconnected.com/managing-react-application-state-with-mobx-full-stack-tutorial-part-1-372a7825847a)

## 进一步阅读

*   学习/介绍
    *   [学习 React 框架的 5 个实例——tutorial zine](https://tutorialzine.com/2014/07/5-practical-examples-for-learning-facebooks-react-framework)
    *   [https://medium . com/in-the-weeds/learning-react-with-create-react-app-part-1-a12e 1833 FDC](https://medium.com/in-the-weeds/learning-react-with-create-react-app-part-1-a12e1833fdc)
    *   [构建 React Redux 应用的逐步指南–rajaraodv–Medium](https://medium.com/@rajaraodv/step-by-step-guide-to-building-react-redux-apps-using-mocks-48ca0f47f9a)
    *   [用 ReactJS 在 30 分钟内制作一款手机 App――Scotch](https://scotch.io/tutorials/make-a-mobile-app-with-reactjs-in-30-minutes)
*   与网络平台的集成
    *   [Fullstack React:如何让“create-react-app”与您的 Rails API 一起工作](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/)
    *   [Fullstack React:如何让“create-react-app”与您的 API 一起工作](https://www.fullstackreact.com/articles/using-create-react-app-with-a-server/)
*   VS 代码
    *   直接从 VS 代码中实时编辑和调试 React 应用——无需离开编辑器🔥 🎉🎈
    *   [React Visual Studio 代码中的 JavaScript 教程](https://code.visualstudio.com/docs/nodejs/reactjs-tutorial)
    *   [使用 VS 代码操作调试 Create React 应用](https://elijahmanor.com/cra-debug-vscode/)
*   混杂的
    *   【Airtable 如何使用 React–马特·布什–Medium

[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 在博客上写我的作业

### 标记 Sta Ana Sep 16 ' 181min read

#study #learning #fullstack](/booyaa/blogging-my-homework-3ohf)
[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 做一只刺猬-第一部分

### 标记 Sta Ana Sep 18 ' 18 15 分钟读取

#learning #study #react #webappdev](/booyaa/web-app-development-core-skill-hedgehog---part-one-2144)
[![booyaa image](../Images/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 像狐狸一样狡猾

### 标记 Sta Ana Sep 28 ' 184min read

#study #learning #react #frontend](/booyaa/web-app-development-core-skill-fox-3ij5)