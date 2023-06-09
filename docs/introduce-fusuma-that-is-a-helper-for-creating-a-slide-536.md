# Fusuma 简介:使用 markdown 创建幻灯片的助手

> 原文：<https://dev.to/abouthiroppy/introduce-fusuma-that-is-a-helper-for-creating-a-slide-536>

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【hiropy】](https://github.com/hiroppy)/[【fusuma】](https://github.com/hiroppy/fusuma)

### ✍️Fusuma 轻松制作带降价功能的幻灯片。

<article class="markdown-body entry-content container-lg" itemprop="text">[![](img/a083d7796ae949b8d65042b726e90414.png)](https://raw.githubusercontent.com/hiroppy/fusuma/master/./site/docs/assets/logo.svg)**A tool to create slides easily for you <g-emoji class="g-emoji" alias="writing_hand" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/270d.png">✍</g-emoji> ️**

[![npm](img/c37d6afb48bf93f6dfb327dbd6b73c9e.png)](https://www.npmjs.com/package/fusuma)[![Azure](img/6d516e0f78137b3a1de9f1968bb67cd3.png)](https://dev.azure.com/hiroppy/fusuma)[![Codecov](img/7bca3fcbff6a5eb83dbf01a448e00eee.png)](https://codecov.io/gh/hiroppy/fusuma)

## 特征

*   零配置
*   提供多种模式
*   降价和 [MDX](https://github.com/mdx-js/mdx)
*   主题
*   代码语法突出显示、MathJax、图表和流程图
*   [浏览器友好的幻灯片](https://raw.githubusercontent.com/hiroppy/fusuma/master/#lighthouse)
    *   灯塔的所有结果都是完美的
    *   完全支持搜索引擎优化和 OGP
    *   正在检查 [a11y(可访问性)](https://developer.mozilla.org/en-US/docs/Glossary/Accessibility)
*   可定制的 JavaScript 和 CSS
*   侧边栏有议程和一些功能

## 模式

*   初始化模式
    *   创建幻灯片、样式、配置文件
    *   创建 GitHub 动作，将幻灯片自动部署到 gh 页面
*   发展模式
    *   和 HMR 一起跑步
    *   只是编码 Markdown 和有时 CSS
*   构建模式
    *   渲染为 html 并优化 js，css，md
    *   生成幻灯片图像为`og:image`并自动检查 a11y
*   [演示模式](https://raw.githubusercontent.com/hiroppy/fusuma/master/#presenter-mode)
    *   演讲者注释
    *   计时器
    *   记录您的页面动作和语音
*   部署模式
    *   部署到 GitHub 页面
*   PDF 模式
    *   将幻灯片导出为 PDF

## 民众

*   [介绍扶桑](https://hiroppy.github.io/fusuma/intro) [ [仓库](https://raw.githubusercontent.com/hiroppy/fusuma/master//packages/playground/intro)
*   [主题](https://hiroppy.github.io/fusuma/themes/) [ [储存库](https://raw.githubusercontent.com/hiroppy/fusuma/master//packages/playground/themes)
*   [数学、图表和流程图](https://hiroppy.github.io/fusuma/advanced/) [ [知识库](https://raw.githubusercontent.com/hiroppy/fusuma/master//packages/playground/advanced)

[![Open in Gitpod](img/036fb817afa4f0df58b50d9ea9f137e0.png)T2】](https://gitpod.io/#https://github.com/hiroppy/fusuma/blob/master/samples/intro/slides/0-title.md)

## 入门指南

```
$ npm i fusuma -D
$
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/hiroppy/fusuma)

# 目标

创建了一个 CLI，支持用一个命令发布，无需编写 webpack、Babel、Postcss 等设置文件。

制作一个只有降价的幻灯片。

它可以快速创建幻灯片并将其发布到 gh-pages。

# 演示

slide:[https://slides . hiropy . me/the-present-and-future-of-JavaScript/](https://slides.hiroppy.me/the-present-and-future-of-JavaScript/)
repo:[https://github . com/hiropy/slides/tree/master/slides/the-present-and-future-of-JavaScript](https://github.com/hiroppy/slides/tree/master/slides/the-present-and-future-of-JavaScript)

幻灯片:[https://hiroppy.github.io/fusuma/fx/](https://hiroppy.github.io/fusuma/fx/)T2【回购:[https://github.com/hiroppy/fusuma/tree/master/samples/fx](https://github.com/hiroppy/fusuma/tree/master/samples/fx)

# 程序

您只需执行以下三行代码来执行、生成和部署幻灯片。

```
$ npm i fusuma -D
$ npx fusuma init 
$ mkdir slides && touch slides/title.md && echo '# Hello😄' > slides/title.md

# --- executable tasks---
$ npx fusuma start    # development
$ npx fusuma build    # production as NODE_ENV=production
$ npx fusuma deploy   # deploy to github pages
$ npx fusuma pdf      # export as PDF from HTML

# --- Tree ---
$ tree -a
.
├── .fusumarc.yml
└── slides
    └── title.md

1 directory, 2 files 
```

Enter fullscreen mode Exit fullscreen mode

执行`npx fusuma start`时，输出如下。
[![](img/c8a7113420242b654aa8d37cc812bf0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QaBFfoMt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hiroppy/fusuma/master/media/procedure-screenshot.png)

默认主题是定制主题星云。

并且只有`.fusumarc.js`和`slides/`是执行时的必要文件。

# 任务

Fusuma 支持以下任务。

*   `init`:生成配置文件(. fusumarc.js)
*   `start`:用 webpack-dev-server 运行
*   `build`:用 webpack4 构建
*   `deploy`:上传预建文件到 gh-pages
*   `pdf`:将预建文件从 HTML 转换为 pdf

# 它能做什么/它不能做什么

## 它能做什么

*   开发、构建和部署可以通过一个命令完成
*   演示者模式
    *   [https://github.com/hiroppy/fusuma#presenter-mode](https://github.com/hiroppy/fusuma#presenter-mode)
*   侧边栏中会创建一个目录
*   可以扩展 css 和 js
*   可以设置 OGP 和 SNS
*   等等…

## 什么事情都可以不做

表达动画很难，因为一张幻灯片是 Markdown 写的。
不过，你可以把幻灯片写成 HTML，扩展 JS。

* * *

如果你对扶桑感兴趣，请看这个资源库😍

[https://github.com/hiroppy/fusuma](https://github.com/hiroppy/fusuma)

谢谢！