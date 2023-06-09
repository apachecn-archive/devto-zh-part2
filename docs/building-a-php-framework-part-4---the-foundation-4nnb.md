# 构建 PHP 框架:第 4 部分——基础

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-4---the-foundation-4nnb>

第 3 部分是关于行动的——实际上没有采取任何行动，也没有写任何代码。这一期将真正看到 Analyze 框架形成的基础。

## 框架结构

我体验过或研究过的大多数框架都采用了一个主“核心”框架和一个示例应用程序的方法。一些例子包括 Slim，它有自己的[核心框架](https://github.com/slimphp/Slim)和[框架应用](https://github.com/slimphp/Slim-Skeleton)。 [CakePHP](https://github.com/cakephp/cakephp) 做的也不错。 [Laravel](https://github.com/laravel/framework) 没有称[为他们的](https://github.com/laravel/laravel)为“范例”或“框架”应用，但实际上确实如此。

我将沿着这些伟大的框架铺设的道路，进行同样的结构分析。下面您将找到每一个的存储库:

[解析 PHP 核心框架](https://github.com/AnalyzePHP/framework)
[解析 PHP 实例/骨架 App](https://github.com/AnalyzePHP/analyze)

目前我将关注核心的 Analyze PHP 框架。毕竟，没有它，就没有理由有一个示例应用程序。

## 目录结构

首先，让我们看一下在撰写本文时 Analyze 目录结构的情况。

```
+ /src
+ /tests
+ .gitignore
+ composer.json
+ LICENSE
+ README.md 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】/src](#src)

目录将包含框架。所有分析器现在和将来都装在这里。

## /测试

顾名思义，这个目录将保存我们对这个框架的所有测试——稍后会详细介绍。

## 。吉蒂尔

文件，以保持不需要的项目回购。了解更多关于。gitignore 文件[这里](https://git-scm.com/docs/gitignore)。

## composer.json

这个文件详细描述了框架的依赖关系和其他信息。点击了解更多信息[。](https://getcomposer.org/doc/04-schema.md)

## 执照

描述框架许可证的文本文件，即 MIT。

## README.md

给我读一下减价文件。目前还没什么进展。

## 测试驱动开发

在构建 Analyze 时，我将尝试使用 [TDD](https://en.wikipedia.org/wiki/Test-driven_development) 。我过去曾经尝试过 TDD，但是我从来没有构建过如此大规模的东西。我将在下一篇文章中更详细地介绍，但是更多的信息请查看下面的“进一步阅读”。

## 进一步阅读

[测试驱动开发(TDD)简介](http://agiledata.org/essays/tdd.html)

谈到 TDD 和 PHP，Chris Hartjes 是其中的佼佼者。我最近读了他的书《最小可行测试》，怎么推荐都不为过。我郑重声明，我不是靠支持坏脾气学习赚钱的。我就是挖掘一下，想分享一下。

[最初发布于 DevelopmentMatt.com](http://developmentmatt.com/building-a-php-framework-part-4-the-foundation/)