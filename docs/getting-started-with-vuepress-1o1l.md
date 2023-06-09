# vexpress 入门

> 原文：<https://dev.to/lauragift21/getting-started-with-vuepress-1o1l>

[![img](img/25bdeba94591eb60f825f60a82c68036.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QeXVVDFy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xnkv5z7k6vl3qs495q1d.png)
*[vue press](https://vuepress.vuejs.org/)是静态站点生成器列表中的新成员。Vue 的创造者尤雨溪想出了这个为基于 Vue 的项目写文档的好工具，这是项目背后的灵感，但是这个强大的工具也可以定制以满足你的需要。*

事实上，VuePress 站点是一个由 Vue、Vue 路由器和 webpack 支持的 SPA。构建完成后，将创建应用程序的服务器渲染版本。类似于 Nuxt 和 Gatsby 的方法。

每个 markdown 文件都用 markdown-it 编译成 HTML，然后作为 Vue 组件的模板进行处理。这允许你在你的 markdown 文件中直接使用 Vue，当你需要嵌入动态内容时，这是非常好的。

### 特性

*   内置降价扩展
*   默认主题
*   自动服务人员
*   谷歌分析集成
*   多语言支持

### 安装

```
// install globally
yarn global add vuepress
npm install -g vuepress

// create a markdown file
echo '# Hello VuePress' > docs/README.md

// start writing
vuepress dev

// build
vuepress build 
```

Enter fullscreen mode Exit fullscreen mode

设置完成后，我们应该配置网站，VuePress 是高度可定制的，它带有默认主题，可以在这里[配置](https://vuepress.vuejs.org/config/)。这是配置 VuePress 网站所需的文件`docs/.vuepress/config.js`

```
module.exports = {
  title: "Welcome to Wakanda",
  description: "The cultured people of Wakanda welcome you"
} 
```

Enter fullscreen mode Exit fullscreen mode

```
---
home: true
actionText: Get Started →
actionLink: /guide/
features:
- title: Simplicity First
  details: Minimal setup with markdown-centered project structure helps you focus on writing.
- title: Vue-Powered
  details: Enjoy the dev experience of Vue + webpack, use Vue components in markdown, and develop custom themes with Vue.
- title: Performant
  details: VuePress generates pre-rendered static HTML for each page, and runs as an SPA once a page is loaded.
footer: MIT Licensed | Copyright © 2018-present Evan You
---
# Wakanda Heritage

## Alert Options

::: tip
This is a tip
:::

::: warning No
This is a warning
:::

::: danger
This is a dangerous warning
::: 
```

Enter fullscreen mode Exit fullscreen mode

对于 markdown 内容，我们可以通过使用 vue 的内置语法和 Vue 组件来实现很多功能。所有的 markdown 文件都被编译成 Vue 组件并由 webpack 处理，因此您可以并且应该更喜欢使用相对 URL 来引用任何资产。

### 覆盖样式

如果你想覆盖样式，你可以通过创建一个`.vuepress/override.styl`来改变默认的主题样式。

```
 $accentColor = #3eaf7c
$textColor = #2c3e50
$borderColor = #eaecef
$codeBgColor = #282c34 
```

Enter fullscreen mode Exit fullscreen mode

### 使用网络部署

以下指南假设您将文件放在项目的 docs 目录中，并使用默认的构建输出位置。
您的`package.json`文件应该包含以下用于构建您的文档的命令。

```
 "scripts": {
  "dev": "vuepress dev:docs"
  "build": "vuepress build:docs"
} 
```

Enter fullscreen mode Exit fullscreen mode

Netlify 是静态网站最好的 CDN 之一，我将在 Netlify 上部署这个 VuePress 项目，从 GitHub 设置一个新项目，设置如下:

```
 Build Command:npm run docs:build or yarn docs: build
Publish directory:docs/.vuepress/dist 
```

Enter fullscreen mode Exit fullscreen mode

点击部署按钮！

[![Wakanda](img/40689c409f142914a80d42cc30777756.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pwxFwUYj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/c_crop/v1524289619/Screenshot_from_2018-04-20_22-33-04_pgvl39.png)

VuePress 是惊人的快速和容易上手，本教程打破了所有 VuePress 能够在一口大小的部分。能够在 markdown 文件中编写 Vue，定制主题，还提供预渲染的 HTML，并在加载时作为 SPA 运行。请随意查看[文档](https://vuepress.vuejs.org/guide/)以了解更多关于 VuePress 的信息。我们最后的项目[住在这里](https://gallant-nobel-d7c6cd.netlify.com/)和[回购](https://github.com/lauragift21/vuepress)。

*也可以随时对 VuePress 发表评论，提出问题或想法。*

最初发布于[giftegwuenu.com](https://www.giftegwuenu.com/2018/04/21/getting-started-with-vuepress/)