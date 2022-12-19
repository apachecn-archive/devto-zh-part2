# GraphQL + CMS =花生酱+果冻

> 原文：<https://dev.to/takeshape/graphql--cms--peanut-butter--jelly-25b>

最近，我有机会在纽约的 JAMstack 会议上介绍使用 GraphQL 和 Headless CMS 的好处。活动由[新动态](https://www.thenewdynamic.org/)在[光影工作室](https://postlight.com/)举办。

[https://www.youtube.com/embed/PDnrde2TyOY](https://www.youtube.com/embed/PDnrde2TyOY)

## 无头 GraphQL CMS + SSG

TakeShape 的 Headless+graph QL+Static Site Generator 组合为 JAMstack 提供了一个非常好的 CMS。这其中有很多术语，所以我将对其进行分解:

### 无头

内容与表现相分离。创作环境完全独立于用户与之交互的网站。由于您的产品是预渲染的 html、css 和 JavaScript，因此您的体验更高效、更安全、更具可扩展性。

### GraphQL

GraphQL 是成形核心。每次您与 TakeShape 上的内容交互时，您都在与 GraphQL 交互

### CMS

一些用户可能觉得与视觉化的东西交互更舒服，所以我们为非技术用户提供了一个管理内容的界面。

### 静态内容生成器(静态站点生成器)

我们构建了一个静态站点生成器，我们认为它更像是一个静态内容生成器。这是一种更低层次的抽象。您可以使用 TakeShape 的静态内容生成器来生成静态网站。但是我们想保持简单。GraphQL 查询+模板= html。我们允许你用自己的构建系统来处理 JavaScript 和 CSS。

### 建模、创建、开发、使用

[![Four Steps to using TakeShape - Model, Create, Develop, and Use](../Images/14fad976cacc12a88bceb87827bcc8c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HFGiWeO1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a6x37dq448f2twrddkc0.png)

## 决定使用 GraphQL

当我们决定围绕 GraphQL API 构建 TakeShape 时，我们考虑了一些利弊

### 弊

*   GraphQL 相对较新，尚未被广泛采用。它的第一个稳定版本是在 2016 年 10 月，我们在 2016 年 8 月开始指定 TakeShape
*   脸书的专利许可条款是限制性的。谢天谢地，这个问题已经解决了。
*   休息是如此的熟悉。我们真的能说服人们使用 GraphQL 吗？

### 优点

*   闪回摔跤休息端点混搭自定义数据。试图将多个 API 结合在一起或者一次从多个实体中检索内容是很棘手的。这正是脸书创建 GraphQL 的原因。
*   与 curl 一起尝试弄清楚 API 是如何工作的。如果你曾经使用过基于 API 的 CMS，并且你不确定你将在你的模板中重新使用什么字段，你知道这种痛苦。

### 最后，归结为几件事

*   和大多数开发人员一样，我们更喜欢使用新技术，而不是旧的和劣质的东西。
*   GraphQL 极大地减少了我们需要编写的代码量，这意味着我们可以更快地构建 TakeShape。
*   GraphQL 的自文档化特性极大地简化了新开发人员学习如何使用 TakeShape 的 API 的体验。因为我们正在构建一个工具，每个人的 API 本质上都取决于他们的环境和他们的创造力，所以学习如何更容易地与我们的 TakeShape 交互是很重要的。
*   最重要的是，我们是自己的试验品。我们发现 GraphQl 查询更容易被人们理解和使用。我们知道我们想要建立一个 CMS 来帮助那些和我们有相似问题的人，并且像我们一样看待这个世界。当您使用 GraphQL 时，它看起来很好，并且很容易理解发生了什么。

```
query  {  homepage  {  title  image  {  path  }  } 
```

```
<html>
<head>
    {{ homepage.title }}
</head>
<body>
    <h1>{{ homepage.title }}</h1>
    <img src="{{ homepage.image.path | image }}"/>
</body>
</html> 
```

## 我们如何使用 GraphQL 来构建 TakeShape

*   TakeShape 是围绕 GraphQL API 构建的。它不仅仅是我们与其他开发者的接口。我们用它来实际构建应用程序。这不是一个螺栓
*   TakeShape 使用我们提供给用户的相同的 GraphQL API 来构建网站，为用于建模和内容创建的单页 react web 客户端、CLI 工具和静态内容生成器提供动力。
*   这是整个应用程序的运行方式。

[![TakeShape's Collaborative Web UI, Static Site Generator, GraphQL QPI, Dev Tools, Hosting Via Third-parties](../Images/af6d7c2963cd55f414bfd2fba695dd80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1KzMh0cZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l73rv0me7qbfekxlf4qg.png)

使用 GraphQL API 构建一个提供 GraphQL API 的 GraphQL CMS。是 Meta！

[![TakeShape is Meta GraphQL](../Images/627d7cbf9fc0cfd0e03deecee88fcca0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XeYaHht0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/da1pbz0t4g3x18y8t7w7.gif)

## 开发者如何使用 TakeShape 的 GraphQL API

*   直接通过 GraphQL 端点发出请求
*   使用 TakeShape 静态内容生成器并编写 GraphQL 查询文件，检索存储在 TakeShape 中的内容并用于生成静态站点
*   或者使用静态站点生成器，比如 Gatsby，它能很好地与 GraphQL 配合使用

[![Directly with the GraphQL API, Using the SSG, Third parties like Gatsby](../Images/97cffadf09cff904867fbf2c945d6ab1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NIbf0NMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aqlf20dshbw89g8nml5q.png)

对无头 GraphQL CMS 感兴趣？TakeShape 是一个无头的 GraphQL CMS，它使得构建 JAMstack 站点更加容易。在 TakeShape，我们正在为最具创造力的设计师和开发人员打造最好的内容管理工具。我们的项目模板让您轻松入门。此外，价格完全可以承受。[注册一个免费账户](https://www.takeshape.io/)，花更多的时间发挥创造力！