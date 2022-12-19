# 到网络包还是不到网络包

> 原文：<https://dev.to/mlichwa/to-webpack-or-not-to-webpack-936>

我正在用固定的预算做一个简单的微型网站。没什么花哨的——bootstrap+jQuery+CSS 和 html。我的第一反应是用 npm 启动一个新项目，然后安装 Webpack。我有几个用 Webpack 构建的工作网站，但我不会称自己精通使用它。每次我使用它的时候，我都必须查找 Webpack 能够做到的所有技巧。加载器:css，scss，字体，html，jpg 资产。带有“$”的全局 jQuery 插件、引导模块导入、js minifiers、sass 和 css minifiers 以及用于连续构建的 Webpack 服务器。

设置它将需要至少 2 个小时从我的预算。拥有代码自动化我会感觉更好，但我不确定客户是否会注意到。此外，使用 Webpack 还假设将来修改代码的人也知道如何使用它...

我想知道使用 Webpack 和简单地用传统方式编码之间的界限在哪里。