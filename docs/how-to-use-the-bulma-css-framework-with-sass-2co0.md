# 如何使用布尔玛 CSS 框架(带 SASS)

> 原文：<https://dev.to/whoisryosuke/how-to-use-the-bulma-css-framework-with-sass-2co0>

布尔玛被其网站描述为基于 Flexbox 的免费开源 CSS 框架。与 Bootstrap 之类的包相比，它比 Bootstrap 或 Semantic 之类的 CSS 系统更轻量级，只有 73kb。它看起来很好，我喜欢页面的美感和它的*图形*性质，相比之下，许多其他更保守，几乎*公司*框架。

如果您对使用默认库感兴趣，只需导入一个 CSS 即可。但是如果你想轻松地改变所有组件的颜色、排版或任何其他设置，使用 SASS 版本要容易得多。这很简单，但是没有像我喜欢的那样被很好地记录下来，所以我写了这篇文章来帮助其他人完成这个过程。

## 安装布尔玛-启动

布尔玛**的好心人已经整理出一个软件包来帮助开发者编译 **SASS** ，而不需要对他们现有的项目做任何额外的配置。您可以安装 bulma-start 包，而不是自己安装`node-sass`并设置`package.json`。**

在项目根目录下运行以下命令:

`npm install bulma-start`

## 如何使用布尔玛-Start

Github repo 的自述文件没有详细说明如何实际使用这个包。幸运的是，[已经有人讨论过这个问题了](https://github.com/jgthms/bulma-start/issues/1)，所以这个问题已经得到了大致的回答。

为了编译 SASS，您必须将终端导航到`node_modules/bulma-start`文件夹，并从那里运行`npm`命令。

1.  `cd node_modules/bulma-start/`
2.  `npm run css-build`

## 但是我拿这些文件做什么？

一旦编译完成，你可以直接包含来自`node_modules`文件夹的 CSS。但我个人把那个文件夹放在了`.gitignore`下，所以不会提交给回购。

我把这一行换成了`bulma-start/package.json`:

`"css-build": "node-sass _sass/main.scss ../../assets/css/bulma.css",`

这会将 SASS 文件编译成项目资产目录中的 CSS 文件。当你设置 NPM 观察时，它也会不断更新那里的 CSS 文件。容易多了。

## 在一天的最后

我不太喜欢布尔玛。它组织得不够好，不合我的口味。我必须将它转换成深色主题，这需要我为每个组件颜色(从背景到链接颜色)找到唯一的 SASS 变量。它允许组件之间更大的定制，但是让大规模编辑风格成为一场噩梦。

他们真的可以从语义用户界面以及他们如何将他们的框架设计成 OOCSS 中得到启示。当我想要一个蓝色的导航栏时，我不需要挖掘布尔玛萨斯变量来寻找`$navbar-background-color`，我只需要包含 CSS 类:`inverted blue`。

奥斯卡保持常规

* * *

**继续阅读:**

*   [布尔玛](https://bulma.io/)
*   [布尔玛启动(SASS 启动套件)](https://bulma.io/bulma-start/)
*   [布尔玛文件](https://bulma.io/documentation/overview/start/)