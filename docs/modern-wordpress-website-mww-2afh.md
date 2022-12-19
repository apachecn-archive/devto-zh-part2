# 现代 WordPress 网站(MWW)

> 原文：<https://dev.to/luc45/modern-wordpress-website-mww-2afh>

从我记事起，我就是一名 WordPress 开发者。

在过去的几年里，我打破了我的 WordPress 泡沫，接触了现代 PHP，如 Laravel 和遵循 PSR-4 的独立项目，SOLID 等，阅读了几本 PHP 和其他编程语言的书籍。

我通过创建名为现代 WordPress 网站(MWW)的开源项目将这些知识移植回 WordPress。

MWW 是建立 WordPress 网站的一种现代方式。

简单、无偏见且强大，这是启动一个新的 WordPress 项目的重要框架。以下是它的一些特点:

*   具有本地 WordPress 功能的简单路由引擎。
*   功能齐全的 MVC。
*   现代而简单的 PHP。
*   PSR-4 自动装弹。
*   集成验收、功能、集成和单元测试(感谢来自 wp-browser 的 Luca Tume)
*   作为插件安装

现代 WordPress 网站(MWW)对于使用 WordPress 的有经验的 PHP 开发人员来说是非常棒的，对于想要把他们的技能提升到下一个水平的中级开发人员来说也是非常棒的。

兜一圈，你会惊讶的:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[Luc 45](https://github.com/Luc45)/[modern WordPress 网站](https://github.com/Luc45/ModernWordPressWebsite)

### 现代 WordPress 网站(MWW)旨在为使用 WordPress 带来良好的 OOP 体验。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/2e6cdc0b365480d01a1f7b7f7d96cfb6.png)T2】](https://camo.githubusercontent.com/002449747212d894e83b05e267a296a3dfe770e9/687474703a2f2f6465762e6c7563617362757374616d616e74652e636f6d2e62722f6d77772d6c6f676f2e737667)

## 现代 WordPress 网站(MWW)

现代 WordPress 网站(MWW)是一个颠覆性的网站建设方式。

它作为一个`mu-plugin`与 WordPress 相结合，提供了一个丰富的 OOP 环境来构建一个干净可靠的应用程序。

### 突出

*   现代 PHP
*   PSR-4
*   刀片模板
*   服务提供商
*   DI 容器( [di52](https://github.com/lucatume/di52) )
*   自动化测试( [wp 浏览器](https://github.com/lucatume/wp-browser))

## 这是给谁的

这是为有经验的开发人员设计的，他们可以完全控制项目，计划尽可能使用最少的插件。如果你打算使用依赖模板结构的插件来覆盖，比如 Events Calendar 或 WooCommerce，你可能不想使用 MWW，因为它根本不使用`themes`文件夹。

## 装置

MWW 作为阿木插件安装。这样我们可以在最早的阶段拦截 WordPress 的请求，并且对应用程序有更多的控制。

开始从事…

</article>

[View on GitHub](https://github.com/Luc45/ModernWordPressWebsite)