# 2018 年尝试使用 ES 模块后的感想

> 原文：<https://dev.to/papaponmx/my-impressions-after-trying-to-use-es-modules-in-2018---3mga>

**TLDR**
的支持仍然是实验性的。在前端应用程序中，大多数东西都像预期的那样开箱即用，但在节点库和用 Enzyme 测试时，它有很多问题。

你现在就可以使用它们，但是我们还没有到那一步。

## 简介

作为一名不断学习的开发人员，我总是试图跟上最新的 ES 特性和 web 上所有相关的技术。

我想告诉你两个我尝试使用它们的项目，以及我遇到的问题。

## 项目 1 反应，Redux Firebase 应用

所以我试着将我的应用从 *create-react-app* 迁移到使用包并支持开箱即用的 ES 模块的东西上。一切都很好，我只是把`index.js`改成了`index.mjs`。我感觉生活在未来，这是我的项目看起来的样子:

[![Tweet screenshot](img/e8ed86e1c6f3316db605359d600c1761.png "Tweet screenshot")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zlNorVnr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qo0qci3t8421v2s5es9r.png)

当我试图集成单元测试时，问题出现了，我不确定 Jest 是否有问题，或者 package 是否需要一些额外的配置。让我感到困扰的是，它应该是一个速度极快、零配置的 web 应用捆绑器(T2)，如果是的话，在很大程度上。我给了它一天，我没能解决它，所以我决定搬到我真正热爱的 [`react-boilerplate`](https://github.com/react-boilerplate/react-boilerplate) 。

## Proyect 2 我的木偶师系列博文的应用

[*好奇的话这里有帖子链接*](https://dev.to/papaponmx/front-end-development-automation-with-puppeteer-part-3-3pl6) 。

如果您还没有检查过，它是一个 NodeJS 应用程序，可以在桌面或 CI 服务器上运行，以便进行可视化回归测试。根据[这个帖子](http://2ality.com/2017/09/native-esm-node.html)，你可能不再需要任何`webpack`或`RollupJS`。

仔细检查[的文档](https://nodejs.org/dist/latest-v10.x/docs/api/esm.html)，从节点`v10.x`开始就有实验支持。如果你不知道那是什么意思，这里有一个[链接和解释](https://nodejs.org/dist/latest-v10.x/docs/api/documentation.html#documentation_stability_index)。

无论如何，唯一需要的是添加一个实验标志，命令看起来像这样:

```
$ node --experimental-modules main.mjs 
```

Enter fullscreen mode Exit fullscreen mode

所以，你在回购中看到的所有文件[，曾经是 *ES 模块*，但是我在尝试实现](https://github.com/papaponmx/visual-regresion-test/) [`pixelmatch`](https://github.com/mapbox/pixelmatch) 时遇到了问题。现在不要误解我，这个库很棒，问题是 ES 模块支持仍然是实验性的。问题是，在我的`index.mjs`中，出口没有被识别为出口。

## 结论

这是一个很好的实验，我认为使用 ES 模块对于大多数简单的应用程序来说是可以的，它们在 React、Parcel 和大多数现代浏览器上运行良好，但是如果你有一些外部库，它可能会崩溃。

我会把它推荐给宠物项目，但不是你下一个 SaaS 创业公司的核心，现在还不是时候。你试过这个或者其他的实验功能吗？怎么样了？

感谢阅读伙计们。干杯。