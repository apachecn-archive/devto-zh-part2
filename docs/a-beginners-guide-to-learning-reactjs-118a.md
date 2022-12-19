# 学习反应堆的初学者指南

> 原文：<https://dev.to/robmarshall/a-beginners-guide-to-learning-reactjs-118a>

了解道具、模块捆绑、项目构建等。我已经开始深入学习 ReactJS 和所有相关的零碎内容(我曾经是一个大口大口的人，现在看起来 Webpack 是一个)。

这篇文章是为了把我发现的所有有用的文章和资源放在一个地方，并希望能帮助到其他人。我的想法是随着我的学习不断地增加。敬请期待！

## 设定场景

### 用什么编辑器？

我一直在我的开发中使用括号，但是由于某些原因，括号错误地显示了 ReactJS。它突出显示不应该显示的部分，而不突出显示应该显示的部分。这意味着是时候改变了。[输入原子。易于添加主题、插件](http://thoughtsandstuff.com/atom-package-theme-setup/)，并与 Github 集成。它与 React 配合得很好，还有一个性感的小东西，叫做[电传打字机](https://teletype.atom.io/)。

[https://atom.io/](https://atom.io/)

### 在 React 中构建项目和命名组件

为了让一个项目可以扩展，并且让团队的其他成员明白你到底创造了什么，这个项目需要被适当地组织。我想从一开始就确保我正在创建的项目结构正确，并且组件以可理解和一致的方式命名。这篇文章对我来说是一个完美的起点。

[https://hacker noon . com/structuring-projects-and-naming-components-in-react-1261 b 6 e 18d 76](https://hackernoon.com/structuring-projects-and-naming-components-in-react-1261b6e18d76)

## 理解道具

### React JS 教程:道具

一篇用简单易懂的方式分解道具的思考文章。对我帮助很大。

[http://www.reactjstutorial.net/props.html](http://www.reactjstutorial.net/props.html)

### 谷歌床单？

这并不是真正的“数据库”,但是这两个链接都是很好的例子，说明了如何使用 JS 将简单的数据发送到 Google Sheets。

[https://github . com/dwyl/learn-to-send-email-via-Google-script-html-no-server](https://github.com/dwyl/learn-to-send-email-via-google-script-html-no-server)

[https://blog . 416 serg . me/building-an-app-using-Google-sheets-API-react-d 69681d 22 ce 1](https://blog.416serg.me/building-an-app-using-google-sheets-api-react-d69681d22ce1)

## 数据库呢？

### 介绍 Firebase 和 React

CSS-Tricks 已经整理了一份出色的指南，介绍如何将 Google[Firebase](https://firebase.google.com/)与 React 集成。

[https://css-tricks.com/intro-firebase-react/](https://css-tricks.com/intro-firebase-react/)

## 模块捆绑，并管理那些任务

### 如何将 Webpack 与 React 一起使用:深入教程

在本教程中，您将看到 React 入门的 Webpack 基础知识，包括 React 路由器、热模块替换(HMR)、按路线和供应商划分代码、生产配置等。

[https://medium . freecodecamp . org/learn-web pack-for-react-a 36 D4 CAC 5060](https://medium.freecodecamp.org/learn-webpack-for-react-a36d4cac5060)

### 创建 React 应用程序中的  CSS 模块& Sass

如何并排使用模块和 Sass

[https://medium . com/@ kswanie 21/CSS-modules-sass-in-create-react-app-37c 3152 de 9](https://medium.com/@kswanie21/css-modules-sass-in-create-react-app-37c3152de9)

## 添加图片

这个难倒我了！为什么我不能像往常一样添加一个图像就完事了。显然事实并非如此。React 还想对图像做一些有趣的处理，如果图像尺寸较小，它会加载 base64 版本。关于速度的一切！下面的两个链接以不同的方式讲述了同样的事情。两者都帮我理清了头绪！

[https://blog . hello js . org/importing-images-in-react-c 76 f 0 dfcb 552](https://blog.hellojs.org/importing-images-in-react-c76f0dfcb552)

[http://interglobal media network . com/post/importing-images-in-react/](http://interglobalmedianetwork.com/post/importing-images-in-react/)

## 保持应用程序快速运行

### 采用 React.js 的渐进式 Web 应用—页面加载性能

移动网络速度很重要。平均而言，更快的体验导致 70%的更长会话和 2 倍以上的移动广告收入。对 web perf 的投资见证了基于 React 的 Flipkart Lite 的三倍在线时间，GQ 的流量增加了 80%，Trainline 的年收入增加了 1100 万英镑，Instagram 的浏览量增加了 33%。

[https://medium . com/@ addyosmani/progressive-web-apps-with-react-js-part-2-page-load-performance-33b 932d 97 cf 2](https://medium.com/@addyosmani/progressive-web-apps-with-react-js-part-2-page-load-performance-33b932d97cf2)

### 减少 CSS-Loader 生成的类名大小

CSS-Loader 是一个出色的插件，允许 CSS 作为模块使用。通过这个设置，它为模块和 css 函数创建随机的名字。问题是这个名字往往很长。

解决办法就在这里！

[https://medium . freecodecamp . org/reducing-CSS-bundle-size-70-by-cutting-the-class-names-and-use-scope-isolation-625440 de 600 b](https://medium.freecodecamp.org/reducing-css-bundle-size-70-by-cutting-the-class-names-and-using-scope-isolation-625440de600b)

## 例子

### 创建测验

我以前的一个客户让我在 WordPress 上做了一个测试。为了解决他们的问题，我不得不疯狂地写 JS。如果我知道 React，我会这么做。

[http://carinyperez.com/create-quiz-react/](http://carinyperez.com/create-quiz-react/)

### 用 React 创建博客

关于使用 React 创建完整博客应用程序的系列教程。非常全面。

[https://code . tuts plus . com/series/creating-a-blogging-app-using-react–CMS-1171](https://code.tutsplus.com/series/creating-a-blogging-app-using-react--cms-1171)