# VueJS 的自举 vs 语义 UI vs 布尔玛

> 原文：<https://dev.to/sambenskin/bootstrap-vs-semantic-ui-vs-bulma-for-vuejs-3dam>

今天，我将介绍和比较三个现代的基于前端 CSS 的 web 和移动框架，以便与 VueJS 2 协同工作。

对于每个框架，我都使用了由第三方编写的配套库，这使得将框架作为 VueJS 项目的一部分更容易使用。

我测试过的版本有:

*   带有[引导程序](https://getbootstrap.com)4 . 1 . 1 . 1 的引导程序-vue 1.5.1(版本 2 即将推出)
*   [Semantic-ui-vue](https://semantic-ui-vue.github.io) 0.2.8 带[语义 UI](https://semantic-ui.com/) 2.3.1
*   [布埃菲](https://buefy.github.io) 0.6.5 与[布尔玛](https://bulma.io) 0.6.2
*   2016 年 5 月 2 日

只要主要版本保持不变，这篇文章应该仍然是相关的。

我已经包含了一些与入门相关的项目的代码示例，但是请不要将此视为完全万无一失的指南，因为为了简洁起见，我没有包含启动新项目的所有内容。

## 先决条件

对于本文，您需要安装 [nodejs/npm](https://nodejs.org/en/) (最好是带有优秀 npx 二进制文件的最新版本)、[包](https://parceljs.org/)和 [VueJS](https://vuejs.org) 。可选地， [vue-cli](https://github.com/vuejs/vue-cli) 对于创建新项目很有用。

## 自举

Bootstrap 在 web 开发方面已经存在很长时间了，最新的版本在他们迄今为止已经制作的优秀框架的基础上有所改进。对很多人来说，这是默认的选择。

第 4 版有许多改进和变化，最明显的是:对 Internet Explorer 8 的支持已被放弃，默认支持 flexbox，从 less 移动到 sass，从像素移动到像 rems 这样的相对单位。自定义建造者，雕象和咕噜都被删除；简单地说，使用 sass 来包含您从 bootstrap 中需要的内容，您可以自由选择自己的图标集和构建工具，尽管推荐使用 npm 脚本。

### 入门

当您使用 vue-cli 开始一个新项目时， [bootstrap-vue 文档](https://bootstrap-vue.js.org/docs/)中的指南快速而简单。如果您有 npm 的最新版本，那么您可以通过运行以下命令将命令的数量减少一个

```
npx vue init bootstrap-vue/webpack-simple your-project-name-here 
```

Enter fullscreen mode Exit fullscreen mode

然后像往常一样

```
cd your-project-name-here
npm install
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

很简单！我更喜欢使用[package](https://parceljs.org/)来避免任何复杂的 webpack 设置，但是 webpack 更广为人知，所以你会得到更多的支持，并且你会得到一个自动生成的配置文件，这对于大多数用例来说应该是很好的，所以没有什么大问题。

### 裁决

很难去反驳那些工作出色的东西。如果你想要大量的社区支持、主题和定制，那么你可以使用 Bootstrap。它没有什么真正的问题，所以如果你对 bootstrap 满意，我不会建议你改变。

主要的缺点是 bootstrap 仍然需要 jquery 来满足 javascript 的所有需求。对于许多人来说，这很好，但是既然我们在谈论使用 Vue，那么为所有的引导交互加载 jquery 并在更现代的 Vue 框架中编写应用程序的其余部分似乎是一种耻辱。这只是一种妥协，你要么接受，不使用任何基于 javascript 的行为，要么选择其他方式。

## 语义 UI

语义 UI 的目标是提供一种更自然的面向语言的方式来设计风格和与你的前端交互。这些类的名字使得理解和记住使用什么样式更容易，例如，“ui 活动按钮”对于语义 ui 活动按钮，还有什么比这更容易的呢？

我不太喜欢他们的文档，安装页面似乎很复杂，然后你只剩下左边的页面列表，不知道从哪里开始。我更希望在安装后有一个合适的入门页面，给你一个容器或网格，然后是一些典型的入门 ui 元素，如标题、段落、按钮等，但这只是个人偏好

### 入门

启动一个新项目的过程比使用 Bootstrap-vue 稍微复杂一点，因为您需要自己启动项目，使用 gulp 构建文件，但实际上这没什么大不了的。语义 ui 的 npm 安装程序有一个非常好的问答驱动系统，可以把文件安装到你想要的地方，但我个人更喜欢一个固执己见的安装程序，它只是把所有东西放在 node_modules 文件夹中通常的位置。它和所有其他的包都是一致的，只是使得在项目之间使用它更加容易。保持简单！

```
npm install semantic-ui-vue --save
npm install semantic-ui --save 
```

Enter fullscreen mode Exit fullscreen mode

这是你的入门索引

```
import Vue from 'vue';
import SuiVue from 'semantic-ui-vue';
import '[path-to-semantic]/dist/semantic.min.css';

/* ... */

Vue.use(SuiVue);

new Vue({
    el: '#app-root',
    render: h => h(App)
}); 
```

Enter fullscreen mode Exit fullscreen mode

那就只是

```
npx parcel 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的问题是，我必须在语义文件夹中运行 gulp watch，以防对我的定制进行任何更改，这使我在项目的其余部分运行 parcel 变得很尴尬。我敢肯定这是一个可以解决的问题，可能只是我安装语义 ui 的地方，所以你可能会有更好的运气。对我来说，这给我留下了不好的印象。我假设您可以编写一个 gulp 文件来查看您的源代码和语义 ui 文件，但是我不想这么做。

### 裁决

我喜欢这种命名法，当你对每样东西都只有简单的英文名字时，会感觉更容易使用。这是一个众所周知的框架，有许多主题和定制，但远远没有 Bootstrap 那么多。至于缺点；手动设置 gulp 或 package 的需要以及使用 jquery 的行为让我倾向于其他方式，但是如果你对此满意并且喜欢更简单的类名，那么我会推荐语义 UI。

## 布尔玛

在我决定比较的三个框架中，布尔玛是最不为人所知的，但这并不困扰我。我真的很喜欢它基于 flexbox，并且没有包含 javascript，所以你可以做一个纯 Vue(或 React)项目。像语义 UI 一样，它们的目标是更容易记住类名和更简单的用法。例如，要使用它们的网格列，只需有一个包含“column”div 的“columns”div，就这样。它们会根据数量自动调整大小，这很棒，也真正凸显了 flexbox 的强大。出于某种原因，他们给一些类加了前缀“is ”,所以你有了“button is-primary is-large ”,它看起来不像语义 UI 那么好，但是没什么大不了的。

### 入门

开始使用 Buefy 的过程很好，也很容易，使用 package 也很好。

```
npm install buefy --save 
```

Enter fullscreen mode Exit fullscreen mode

那么您的 index.js 文件可以这样开始

```
import Vue from 'vue'
import Buefy from 'buefy'
import 'buefy/lib/buefy.css'

Vue.use(Buefy)

new Vue({
    el: '#app',
    render: h => h(App)
}); 
```

Enter fullscreen mode Exit fullscreen mode

定制主题也很容易，只要记得删除上面的第三个导入，因为它在你的 App.vue 文件中被替换了。因为它是你的应用程序的一部分，所以没有必要单独运行 gulp 或其他任何东西来打包。

### 裁决

我真的很喜欢 Buefy，它很容易设置和使用，但对我来说，主要的缺点是 Buefy 中只实现了一个 Bulmo 的子集，所以对于一些代码，您将使用 Buefy 组件，如

```
<b-checkbox>A checkbox!</b-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

但是对于其他人来说，你将使用 div 上的类，例如

```
<div class="container">...</div> 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是因为他们没有任何 javascript 需求，或者该项目还没有实现它们，但是就目前情况来看，我并不热衷于两者的混合，如果有一组一致的组件可以使用就好了。

## 整体

对于大多数项目来说，这三个框架都是很好的选择，所以这取决于个人偏好。对我来说，我喜欢 Bumla/Buefy 的简单性，所以我打算尝试一下，我会在未来更新，让你知道它是如何进行的。

非常感谢您阅读这篇文章！如果你喜欢它，请评论让我知道，或者如果你有任何改进的建议；请务必点击下面的心形/独角兽/书签按钮，我将不胜感激！