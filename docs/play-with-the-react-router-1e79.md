# 玩反应🐘路由器

> 原文：<https://dev.to/sait/play-with-the-react-router-1e79>

玩反应路由器游戏。

# 什么是 React 路由器？

React 路由器帮助我们在 React 应用程序中进行路由，React 路由器中的一切都是基于组件的。

#### 是动态路由还是静态路由？

*   动态路由。

### React 应用中实现路由有什么要求？

*   首先，我们需要安装名为 *react-router-dom* 的包。

```
npm i -s react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

# 现在开始玩吧。

在 src 目录下创建一个 routes.js 文件，如下图所示。

[![](../Images/889f75b8b3cf564ff81c4f9b09451405.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--brZ646J4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r5fw2gkrqiwema66oqka.png)

在 routes.js 文件中，我们需要导入 React 和路由组件。

[![](../Images/9acf69c572b514204f9b1716437fc2b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iD4-Q7G5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0c7zn0bbh0wojnp8expo.png)

然后我们需要导入他们需要路由的组件。

[![](../Images/24bfd6665356a7ff069cb02dcbe9aa3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k-uLYo9i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nvptj8trf6w397j1op33.png)

如上图所示，我导入了应用程序组件和文章组件。

让我们用路由定义一个新组件。

[![](../Images/deb596e7291c241faeea964877b35bb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zr2rmibn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b4k89mgbcgc4ebqdm3vn.png)

上面的代码是什么.....

我觉得它看起来很有趣。

**链接:**链接组件帮助定义导航。
**路由:**路由组件帮助定义路由。
**准确:**当我的网址为 **/** 时，请准确加载 App 组件

首先，我们需要包装路由器组件。

就这样，我们完成了路由。

现在，在 index.js 中，我们需要添加这些 Routes 组件。

[![](../Images/8ad6ddd14b5cf1253c6d0b9cfdd2a91b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--osz6Gchy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27osuxxqwaq3q65gk68u.png)

打开您的 App.js 文件或您在路由中使用的组件，将 console.log(this.props)添加到如下图所示的代码中。

[![app.js](../Images/b61bc2daa3c4a1739d7c8ee233f2bd39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1X3A14rQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzu5diah1l7x0kphek10.png)

现在运行您的开发服务器。

你在你的浏览器控制台里看到什么了吗？

[![](../Images/658ef11d71d0f8998ae7c58d3405a179.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EuQp_Bxf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uur51kh6mzbxm3v928d3.png)

##### 我们的组件里有一个可用的对象。

## 谁给了我们这个对象？

答案是，每当我们为组件声明路由时，React router 都会将对象传递给组件。

例如，我们为 App 组件和 Posts 组件定义了 routes，这样这些组件现在就知道上图中的这些对象了。

## 用 React 路由器编程导航。

这个名字告诉我们如何以编程的方式导航，意思是每当一个程序运行或者一些事件发生的时候，把我导航到某个路径。

例如在提交表单时。

我已经展示了一个可用的历史属性。

我们甚至可以用其他方式来完成，但我是借助历史属性来展示的。

[![](../Images/72d09ee71b6ad2f0fcb0b8c5f1d94747.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fyTIKemV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0mbb75p2oid4qhuea9y0.png)

最终输出。

[![](../Images/d5615b75f44d6234b3ee8fcca2ab4030.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3YLlsE2Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1rn4r8etyk2m4bc2nl90.gif)

### [玩 React 路由器 Part-2](https://dev.to/saigowthamr/play-with-react-router-part-2-4i8m)

快乐编码...

[React 路由器初学者指南](https://reactgo.com/reactrouter)