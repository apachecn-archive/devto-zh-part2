# 关于创建 React 应用程序，我不知道的 5 件事

> 原文：<https://dev.to/progresstelerik/5-things-i-didnt-know-about-create-react-app-49nj>

寻找使用 Create React App 的技巧？这里有五个你可能不知道的特点。

[Create React App](https://github.com/facebook/create-react-app) 是一款让创建 React 应用变得非常简单的工具，无需处理复杂的配置。最近发布的 [Create React App v2](https://dev.to/progresstelerik/hello-create-react-app-20-316-temp-slug-3014493) 是再次浏览他们的[用户指南](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md)的一个很好的借口，并找到我不知道的有趣功能。以下是我的亮点。

## 1。在编辑器中显示 Lint 错误

我喜欢棉绒！它们帮助我在编写代码时识别潜在的问题，甚至在我有机会运行它之前。Create React 应用程序已经安装了 [ESLint](https://eslint.org/) ，并且默认配置了一些规则，但它只在终端中显示林挺警告和错误:

[![React](../Images/560ee98c0ab719e920ae8990273872a2.png "5didnt-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xq2Ar2ND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/5didnt-1.png%3Fsfvrsn%3Dd1e4cbae_1)

我真正想要的是在我的编辑器中直接看到那些警告和错误，这样我就可以立即修复它们，而不必切换上下文。

事实证明，Create React App 使得在项目的根目录下添加一个. eslintrc 文件变得非常简单，其内容如下:

`{`

``"extends": "react-app"``

 ``}`

如果您正确配置了编辑器(我使用 VSCode 的 [ESLint 扩展)，那么您将立即看到结果:](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

[![React](../Images/7b168a3740e3e7b2b61904b07362a8dc.png "5didnt-2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X_5yM5Na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/5didnt-2.png%3Fsfvrsn%3D9b979dc0_1)

## 2。使用更漂亮的自动格式化代码

是一个固执己见的代码格式化程序，在我们所有的文件中强制执行一致的风格。我已经开始在我所有的项目中使用它，因为它让我专注于代码本身，忘记格式化。

您可以从命令行运行它(用 NPM install-global appellister 安装它，然后在您的项目中运行 appellister)或从您的编辑器运行它(我使用 VSCode 的[appellister 扩展)。但是另一种流行的运行方式是通过 Git 钩子。](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

如果你从未听说过钩子，它们是 Git 在特定动作发生时运行的脚本。例如，在创建提交本身之前，每次执行 git 提交时都会运行一个预提交挂钩。我们可以从一个预提交钩子调用 pre-commit 来格式化我们所有的暂存文件，并确保我们提交给 repo 的所有内容都被正确格式化。

虽然我们可以手工编写钩子(看看您。git/hooks 文件夹查看一些例子)，有两个 npm 模块帮助我们完成这个过程， [husky](https://github.com/typicode/husky) 和 [lint-staged](https://github.com/okonet/lint-staged) ，它们与 Create React 应用程序完美集成。

让我们安装更漂亮的和那两个模块:

`npm install --save-dev prettier husky lint-staged`

然后，我们将在应用程序的 package.json 文件末尾添加以下部分:

`{`

``// ...``

 ```"husky": {``

 ```"hooks": {``

 ```"pre-commit": "lint-staged"``

 ```}``

 ```},``

 ```"lint-staged": {``

 ```"src/**/*.{js,jsx,json,css}": [``

 ```"prettier --write",``

 ```"git add"``

 ```]``

 ```}``

 ``}`

现在，每次我们提交时，我们都会看到 husky 调用 lint-staged，而 lint-staged 又会在我们将要提交的所有文件上调用 prettier。

[![React](../Images/d0d3d1a2f63316a8953e18d3ca82ead0.png "two")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---JqXjbiv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/two.gif%3Fsfvrsn%3Db8b5b775_1)

很整洁，是吧？

## 3。孤立地开发组件

如果我们正在处理一个复杂的应用程序，它有许多组件，每个组件都有不同的状态，每次我们做了一个改变，我们都必须重新加载整个应用程序，并与之交互，直到我们得到想要的状态。

另一种不同的工作方式是使用工具，如[故事书](https://storybook.js.org/)和 [Styleguidist](https://react-styleguidist.js.org/) ，它们允许我们独立开发每个组件。

我特别喜欢 Storybook，因为将它与 Create React 应用程序集成在一起非常容易:

`npm install --global @storybook/cli`

`getstorybook`

[![React](../Images/1e2c89dce057b7f5bb59fca8c3c5e071.png "5didnt-3")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1hTekAKT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/5didnt-3.png%3Fsfvrsn%3D4fd33a8e_1)

向导完成工作后，我们只需要运行 npm run storybook，并开始为向导创建的 stories/文件夹中的组件编写故事。

我们可以为我们的 Header 组件添加一个新故事，如下所示:

`import React from 'react';`

`import { storiesOf } from '@storybook/react';`

`import Header from '../Header';`

`storiesOf('Header', module)`

``.add('default theme', () => <``Header`T2】`

 ```.add('light theme', () => <` `Header` `theme` `=` `"light"``

 ```.add('dark theme', () => <` `Header` `theme` `=` `"dark"``

 `这将在我们的故事书中创建一个名为 Header 的新部分:

[![React](../Images/9c9bfbdd6118c1715dd1ade4c1b1b652.png "5didnt-4")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t0CZJ8Yu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/5didnt-4.png%3Fsfvrsn%3De6ff72bd_1)

然后我们可以从那里继续开发它！

## 4。制作渐进式 Web 应用程序

您的应用被视为 PWA 的唯一要求是:

1.  它必须在 HTTPS 上空送达

2.  它必须提供一个清单

3.  它必须注册一个服务工作者

您可能已经通过 HTTPS 提供了您的应用程序，所以剩下要做的事情只有清单和服务人员。

幸运的是，Create React App 已经为我们生成了一个清单，位于 public/manifest.json。

它还会生成一个 ServiceWorker，但是由于用户指南中列出的原因，默认情况下不会注册它[。阅读完该部分并理解他们的推理后，如果您想继续，请打开 src/index.js 并查找以下内容:](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#why-opt-in)

`// If you want your app to work offline and load faster, you can change`

`// unregister() to register() below. Note this comes with some pitfalls.`

`// Learn more about service workers: http://bit.ly/CRA-PWA`

`serviceWorker.unregister();`

现在把 serviceWorker.unregister()变成 serviceWorker.register()就大功告成了。你有一个 PWA，Chrome 会让你的用户把它添加到他们的主屏幕上！

[![React](../Images/48072652dda3302d75d41d43bc98dec5.png "5didnt-5")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sz_3NJoD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/5didnt-5.png%3Fsfvrsn%3D4fdf9260_1)

## 5。代码分割

代码分割是现代 JavaScript bundlers 的一个特性，它允许你把你的应用程序分割成更小的块，然后按需加载。

Create React App v2 支持通过动态导入()语句进行代码拆分。也就是说，如果它遇到调用 import('。/someModule’)在构建你的应用时，它会为 some module 及其所有依赖项创建一个新的块，与你的入口包完全分离。

让我们看一个例子。假设我们有一个复杂的表单，只在用户点击按钮时显示。我们可以使用代码分割来避免在页面加载时下载、解析和执行所有代码，而是等到用户单击该按钮时再加载表单。

下面是我们使用 [formik](https://github.com/jaredpalmer/formik) 和 [yup](https://github.com/jquense/yup) 的复杂表单:

`import React, { Component } from "react";`

`import { Formik } from "formik";`

`import * as Yup from "yup";`

`const formValidator = Yup.object().shape({ /* ... */ });`

`export default class Form extends Component {`

``render() {``

 ```return (``

 ```<``Formik`T2】`

 ```{/* ... */}``

 ```</``Formik`T2】`

 ```);``

 ```}``

 ``}`

这是我们使用动态导入()按需加载表单的应用程序:

`import React, { Component } from "react";`

`export default class App extends Component {`

``constructor() {``

 ```super();``

 ```this.state = {``

 ```Form: undefined``

 ```};``

 ```}``

 ```render() {``

 ```const { Form } = this.state;``

 ```return (``

 ```<` `div` `className` `=` `"app"``

 ```{Form ? <``Form``/> : <``button``onClick={this.showForm}>Show form</``button`T6】`

 ```</``div`T2】`

 ```);``

 ```}``

 ```showForm = async () => {``

 ```const { default: Form } = await import("./Form");``

 ```this.setState({ Form });``

 ```};``

 ``}`

只有当用户点击按钮时，我们才需要支付加载表单的费用。一旦 import() promise 解析完毕，我们调用 setState 并使用加载的组件强制重新呈现应用程序。

[![React](../Images/c804135794645b65ea41bf38284a3b33.png "one")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cQMp3l95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/one.gif%3Fsfvrsn%3Ddd4dbb7_1)

如果您仔细观察正在发出的网络请求，您会注意到在我们单击按钮后，有两个新的块(0.chunk.js 和 1.chunk.js)被请求。它们包含 Form 及其依赖项 formik 和 yup，所以我们设法避免在初始页面加载时下载所有代码，使我们的应用程序感觉更快！

## 包装完毕

Create React App 是一个非常棒的工具，可以让你非常容易地开始使用 React。它还包含大量的特性，所以阅读它的文档以获得它的所有好处是值得的。

* * *

**有关使用 React 构建应用程序的更多信息:**查看我们的 [All Things React](https://www.telerik.com/blogs/all-things-react) 页面，该页面提供了广泛的信息和 React 信息的链接——从热门话题和最新信息到如何开始使用和创建引人注目的 UI。