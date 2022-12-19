# 小吃@控制台直观的基于浏览器的 SQL 控制台

> 原文：<https://dev.to/mazentouati/snacksconsole-intuitive-browser-based-sql-console-2bci>

嘿，伙计们

最近我一直忙于创建一个名为“零食项目”的开源项目。这是一个直观的数据库工具包，使数据库管理过程更快、更愉快。

今天，我要和大家分享这个项目的第一个模块。这是一个基于浏览器的控制台，用于运行和保存 SQL 查询。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [顺天](https://github.com/sunchayn) / [小吃——控制台](https://github.com/sunchayn/snacks-console)

### 基于浏览器的直观 SQL 控制台，用于运行和保存 SQL 查询。

<article class="markdown-body entry-content container-lg" itemprop="text">

## mazentouati/小吃-控制台

[![GitHub (pre-)release](img/c666e70a54b9e77c4399628fb1f21bc5.png)](https://github.com/mazentouati/snacks-console/releases/tag/0.1.0)[![Scrutinizer Code Quality](img/5fcd9e1b04d5400c6fbc47b610cbc5fa.png)](https://scrutinizer-ci.com/g/mazentouati/snacks-console/?branch=master)[![StyleCI](img/c22720b9a07bbc10db897055b3605df2.png)](https://styleci.io/repos/157928738)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)T11】](https://github.com/sunchayn/snacks-console./LICENSE)

基于浏览器的直观 SQL 控制台，用于运行和保存 SQL 查询。

[![demo](img/82206dfd5592a4452ea6d2bd9fbefb2a.png)T2】](https://camo.githubusercontent.com/b6fb795c3a0789c42befd7e23b7c23e43cef74da03ca989f196e8fb9d236a61e/68747470733a2f2f6d617a656e746f756174692e6769746875622e696f2f736e61636b73636f6e736f6c652f7075626c69632f7374617469632f73637265656e73686f74732f736e61636b73636f6e736f6c652e676966)

## 使用指南

零食@控制台是一个基于 PHP 的应用程序。在使用它之前，您必须确保您的系统已经准备好运行 PHP 应用程序。所有的网络服务器，如[拉冈](https://laragon.org/)(推荐)或 [XAMPP](https://www.apachefriends.org) 在一个包中提供了运行基于 PHP 的应用程序的所有需求。

在你设置好你的网络服务器后。打开 web 服务器根目录中的命令行，通常是`www`，并按照安装过程进行操作

### 通过作曲家

我们建议通过 [composer](http://getcomposer.org/) 安装这个包:

```
composer create-project mazentouati/snacks-console "console"
```

Enter fullscreen mode Exit fullscreen mode

注意:确保你的网络服务器启用了重写引擎。

## 计划

这是一个初始版本，它只是一个测试和增强当前体验的原型。我们计划增加以下功能:

### 增强查询支持

目前，控制台支持以下命令:

*   挑选
*   更新
*   删除
*   插入
*   …

</article>

[View on GitHub](https://github.com/sunchayn/snacks-console)

## 特性

*   目前控制台支持以下命令: (选择、更新、删除、插入、使用`database`)
*   书签收藏查询
*   内置终端命令
*   选择查询的简洁表表示

## 使用指南

零食@控制台是一个基于 PHP 的应用程序。在使用它之前，您必须确保您的系统已经准备好运行 PHP 应用程序。所有的网络服务器，如[拉冈](https://laragon.org/)(推荐)或 [XAMPP](https://www.apachefriends.org) 在一个包中提供了运行基于 PHP 的应用程序的所有要求。

在你设置好你的网络服务器后。打开 web 服务器根目录中的命令行，通常是`www`，并按照安装过程进行操作

### 通过作曲

我推荐通过[作曲](http://getcomposer.org/) :
安装这个包

```
composer create-project mazentouati/snacks-console "_folder_name" 
```

Enter fullscreen mode Exit fullscreen mode

注意:确保你的网络服务器启用了重写引擎。

然后通过您的浏览器访问您的`"_folder_name"`文件夹，初始设置将弹出来设置您的数据库凭证，然后将您重定向到控制台界面。

## 未来

我计划增强当前的体验，因为当前的版本在某种程度上是研究用户反馈和建议的原型。不过，我将添加以下功能:

#### 扩展命令支持

理论上，它可以执行任何 SQL 命令，但我计划通过支持控制台中的交互式插入来使像`Create`或`Alter`这样的 DDL(数据定义语言)的体验更容易获得。

#### 添加语法高亮

此外，我计划为 SQL 命令添加语法高亮

#### 增强对提取数据的操作

目前，使用`select`命令时显示的数据不是交互式的。我计划重新调整表格的大小，单元格可以点击(点击时显示整个列的数据)，以及任何需要使它更具交互性的东西。

## 文档

不幸的是，我被小吃项目和它的子项目淹没了。我将逐步增加文档，测试源代码和应用程序。

听到你的建议和想法，我真的很激动。

和平。