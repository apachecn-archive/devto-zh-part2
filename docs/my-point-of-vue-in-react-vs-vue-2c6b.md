# 我在 React 与 vue 中的“Vue”观点

> 原文：<https://dev.to/ycmjason/my-point-of-vue-in-react-vs-vue-2c6b>

> 原帖:[https://www.ycmjason.com/blog/2018/05/09.html](https://www.ycmjason.com/blog/2018/05/09.html)

说到选择框架/库，我个人会考虑以下几点。

1.  他们的能力
2.  和他们一起发展的经历

## 能力

就能力而言，他们都有能力做同样的事情。他们都利用虚拟世界。并且具有相似的组件概念。Vue 能做但不能反应的一件事是`<transition>`组件。这使得构建动画用户界面更加简单，并且给你更多的控制权。(查看更多[https://vuejs.org/v2/guide/transitions.html](https://vuejs.org/v2/guide/transitions.html))事实上，由于 Vue 在动画和过渡方面的能力，我怀疑这就是脸书用 Vue 建立这个网站的原因。([https://newsfeed.fb.com/?lang=en](https://newsfeed.fb.com/?lang=en)

## 发展经验

就开发体验而言，我会说 Vue 比 React 有优势。

### 不太陡峭的学习曲线

普遍认为 Vue 的学习曲线不太陡。主要是因为出色的书面文档和指南以及框架的简单性。

此外，Vue 确实是反应性的。`this.a = "hello world"`会改变显示文本。而在 React 中，你必须做`this.setState((prevState, props) => {...})`，这不是很直观。

Vue 也更容易设置。vue-cli 3 为创建新项目提供了一个开发人员友好的界面。创建的项目有其开发依赖性(例如，测试框架、捆绑器、linters...)作为插件安装。这背后的理念是允许 vue 项目轻松地与标准保持同步。假设有一天 Vue.js 决定使用 parcel，而不是 webpack，作为他们推荐的模块捆绑器。现有项目可以无缝切换到 parcel，只需更新项目中的`bundler-plugin`,而无需担心删除旧堆栈、安装新堆栈和配置它们。(看这个关于 vue-cli 3.0 的视频:[https://youtu.be/TRJMT9yjONQ?t=10m18s](https://youtu.be/TRJMT9yjONQ?t=10m18s))

Vue 对现有项目也非常友好。您可以在许多方面使用 Vue，从包含`<script>`标签到使用像 webpack/parcel/rollup 这样的捆绑器。事实上，它是如此灵活，使得 Vue 成为一个很好的候选，可以包含到现有的项目中，而不需要引入额外的构建步骤。

Gitlab 是用 Vue 组件替换部分网站的公司之一。在这篇文章中了解更多:[https://about.gitlab.com/2016/10/20/why-we-chose-vue/](https://about.gitlab.com/2016/10/20/why-we-chose-vue/)

### 组件范围的现成 CSS

React 也有可能，但是 Vue 处理的方式简单明了。因此更好的开发体验。

### html、js、css 完全分离

虽然在同一个文件(`.vue`)中，但是三个部分在三个标签(`<template>`、`<script>`、`<style>`)中清晰直观的分开。不太可能有人会把它们混在一起。而在 React 中，由于 JSX，人们可以很容易地将这三者混合在一起，产生更难维护的代码。这经常让我想起我早期用 PHP 编程的时候，那时我还没有足够的经验。这个问题可以通过遵循好的标准来解决，但是对于我们来说，去做一些几乎不会发生这个问题的事情不是更容易吗？

### Vue 有更多的官方库

路由器和商店是开发大规模应用程序必不可少的两个主要功能。React 有`react-router`和`redux`，但都不是 React 的官方库。(Redux 已被添加到官方图书馆，因为脸书招募了 Redux 的创造者。)另一方面，Vue 有`vue-router`和`vuex`这两个官方库。

## 嗯...

Angular.js 刚出来的时候我是它的粉丝。然后切换到 Angular 2。然后，当 Angular 2 变得太复杂而无法使用时，开始使用 React。爱过的 React + Redux。React Redux 的许多概念确实令人振奋。然后去年夏天发现了 Vue.js，我就爱上了。目前是一个官方 Vue 项目 VuePress 的积极贡献者。([https://github.com/vuejs/vuepress](https://github.com/vuejs/vuepress)

有些人更喜欢使用 JSX，“用 Javscript 做所有的事情”。我尊重他们的意见，但我真的希望他们只有在尝试了不同的选择后才能做出决定。否则你可能会错过你下一个最喜欢的东西。