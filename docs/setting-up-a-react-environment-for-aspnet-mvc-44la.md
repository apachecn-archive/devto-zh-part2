# 为 ASP.NET MVC 设置一个 React 环境

> 原文：<https://dev.to/dance2die/setting-up-a-react-environment-for-aspnet-mvc-44la>

*照片由[Zoltan·塔斯](https://unsplash.com/photos/6vEqcR8Icbs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/atom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 上拍摄*

我有机会使用 AngularJS(是的，第一个版本)更新了一个遗留的 ASP.NET MVC 网站，以使用 Webpack & Babel 7(以前使用脚本标签导入 AngularJS 文件)。

前一篇文章[为 ASP.NET MVC 5](https://dev.to/dance2die/setting-up-an-es6-environment-for-aspnet-mvc-5-11mj-temp-slug-5475884)设置 ES6 环境有点过时，因为它使用的是旧版本的 babel 和 webpack，所以我决定写更简洁的文章来开始使用最新的库。

由于我已经转移到 React 上，我将向您展示如何为 ASP.NET MVC 5 设置 React 环境。

## 🧐先决条件

我会假设你熟悉 NPM 和网络包，

，所以我不会进入 NPM &网络包的每个选项太多的细节。

## 👣设置步骤

1.  创建一个 ASP.NET MVC 网站
2.  创建和配置 NPM 配置文件(package.json)
3.  创建并配置 Babel 配置文件(。babelrc)
4.  创建和配置 Webpack 配置文件(webpack.config.js)
5.  安装 NPM 软件包
6.  安装 Visual Studio 扩展(NPM 任务运行器)

#### 1。创建一个 ASP.NET MVC 网站

创建一个新的 ASP.NET MVC 项目(选择你的选择。NET 框架)。

<figure>[![](img/848d37046d0b074731fd639a72e8902e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CzZnhoOx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/10/1.1-create-a-new-project.jpg%3Fw%3D1170%26ssl%3D1) 

<figcaption>创建新的 ASP.NET MVC 项目</figcaption>

</figure>

并选择一个模板。

<figure>[![](img/ba3a92873f94ed39e6faf4a56e6129dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LptlhHWD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/10/1.2-select-template.jpg%3Fw%3D1170%26ssl%3D1) 

<figcaption>MVC 模板</figcaption>

</figure>

#### 2。创建&配置 NPM 配置文件(package.json)

在项目根中添加一个新项。

<figure>[![](img/b99b1ca630867f722d6a8c13c49523ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eQiQzp_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/10/2.1-create-a-new-item.jpg%3Fw%3D1170%26ssl%3D1) 

<figcaption>添加新物品…</figcaption>

</figure>

创建 NPM 配置文件，`package.json`。

<figure>[![](img/cb44117d96e499c1273c0e829feaa1ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tgwbyHyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/10/2.2-add-package.json_.jpg%3Fw%3D1170%26ssl%3D1) 

<figcaption>npm 配置文件</figcaption>

</figure>

并添加脚本部分。和`package.json`最初看起来如下。