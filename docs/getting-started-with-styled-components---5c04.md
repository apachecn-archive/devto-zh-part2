# 开始使用样式组件💅

> 原文：<https://dev.to/spences10/getting-started-with-styled-components---5c04>

我们将使用样式化组件对基本的 create react 应用程序进行样式化，如下所示:

[![](img/c32b105592b7c03a9bfab5568b1dcf5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x22yK9nL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5dwv10zpqa13wb4pr47l.gif)

但是首先，preamble✨:，我一直在为设计网站而努力，这似乎是开始网站开发的一个方面，要么是事后想起的，要么是被掩盖的。直到去年 12 月，我都不喜欢用 CSS 来设计任何东西，这是一件苦差事，而不是我喜欢做的事情。

直到我开始使用样式化组件，当我加入了一个为[Chingu](https://medium.com/chingu)voyage([`grad.then()`](https://github.com/chingu-voyage3/grad.then/)如果你感兴趣的话)的构建学习项目，我们决定使用 CSS-in-JS 包，我团队中的 [Marina](https://twitter.com/mar_biletska) 在观察组件是如何被样式化的过程中给了我很大的启发，并真正给了我开始使用样式化组件的信心。

###### 我跟 css 以前

[![](img/71f8842528391e0ac505a0ce203d917c.png)T2】](https://i.giphy.com/media/2rj8VysAig8QE/giphy.gif)

我想分享我到目前为止通过设计一个基本的 react 应用程序所学到的东西。

这篇文章中有一些基本的 CSS 概念，在开始讨论样式化组件之前我并不知道，我认为这些组件是在设计网页样式时假定的。

网站的 body 元素的样式是假定的，所以当你从一个空白画布开始时，你添加到你的网站的所有现代 web 浏览器都有一些默认设置，比如保持 16px(或 1rem)或`box-sizing:` `border-box;`的字体大小，也有一些包为你处理这些。

## 版本:

本指南正与以下依赖版本一起使用。

react:16 . 2 . 0
react-DOM:16 . 2 . 0
react-scripts:1 . 1 . 0
style-components:3 . 1 . 6

### 安装样式化组件

好的，让我们引导您在使用 [Create react App](https://github.com/facebook/create-react-app#create-react-app-) 和 [`npx`](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) 时获得的基本 React 应用程序，如果您已经全局安装了 Create React App，那么您可以使用不带`npx`的命令。

```
npx create-react-app style-with-styled-components
cd style-with-styled-components/
npm i styled-components 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了可以进行样式化的基本应用程序，谢天谢地 [Dan](https://github.com/gaearon) 好心地为我们提供了开始样式，所以让我们开始将它们用于样式化组件。

CRA CSS 的布局方式假设每个组件都有一个相应的 CSS 文件，这有助于维护 CSS，并符合 React 将所有文件分成不同组件的思想。

我们可以从`App.js`文件及其伴随的`App.css`文件开始。让我们先来看看`App.js`: