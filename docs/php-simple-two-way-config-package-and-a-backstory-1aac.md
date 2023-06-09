# PHP 简单的双向配置包，和一个背景故事

> 原文：<https://dev.to/mazentouati/php-simple-two-way-config-package-and-a-backstory-1aac>

嗨，伙计们

我很高兴介绍我的第一个开源项目。这是一个基于 PHP 的双向配置包。这个包旨在使源文件与运行时值的同步变得容易。它适用于需要使用基于文件的系统来存储首选项或配置的应用程序。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [顺天](https://github.com/sunchayn) / [简单双向配置](https://github.com/sunchayn/simple-2way-config)

### 基于 php 的简单双向(读/写)配置

<article class="markdown-body entry-content container-lg" itemprop="text">

## mazentouati/简单 2 路配置

[![GitHub (pre-)release](img/9a04ecf17c754ae63aab825a28e83b18.png)](https://github.com/mazentouati/simple-2way-config/releases/tag/0.1.0)[![Build Status](img/ee4910b22090ce9084fae8d282c508aa.png)](https://travis-ci.org/mazentouati/simple-2way-config)[![Scrutinizer Code Quality](img/7da5dc8156f852e6f74228d2ae6e9517.png)](https://scrutinizer-ci.com/g/mazentouati/simple-2way-config/?branch=master)[![Codecov branch](img/f3855f2daba1c52f1ab2659ccf2ebd94.png)](https://codecov.io/gh/mazentouati/simple-2way-config)[![StyleCI](img/c22720b9a07bbc10db897055b3605df2.png)](https://styleci.io/repos/157292080)[![Maintainability](img/a13bda333657b5811cc0ff7317fea192.png)](https://codeclimate.com/github/mazentouati/simple-2way-config/maintainability)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/sunchayn/simple-2way-config./LICENSE)

简单的双向配置是一个基于 php 的读写配置库。它适用于需要使用文件系统来存储首选项或配置的应用程序。

## 装置

我们建议通过 [composer](http://getcomposer.org/) 安装这个包:

```
composer require mazentouati/simple-2way-config
```

Enter fullscreen mode Exit fullscreen mode

## 使用

使用它最简单的方法是通过包的工厂。工厂的必需参数是保存配置文件的目录的路径。

```
use MazenTouati\Simple2wayConfig\S2WConfigFactory
$config = S2WConfigFactory::create( __DIR__ . '/demo' );
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用点标记“{filename}.path.to.value”来访问配置值

```
$host = $config->get('database.drivers.mysql.host');
```

Enter fullscreen mode Exit fullscreen mode

*注意:你的配置文件应该是基于数组的配置，查看这个[例子](https://github.com/sunchayn/simple-2way-config#configuration-file-formats)T3】*

## 应用程序接口

config API 实现了`S2WConfigInterface`。

下面的例子假设你已经将你的配置赋值给一个名为`$config`的变量

### get(string $path，mixed $default =

…</article>

[View on GitHub](https://github.com/sunchayn/simple-2way-config)

### 用法举例

```
use MazenTouati\Simple2wayConfig\S2WConfigFactory;

$config = S2WConfigFactory::create( __DIR__ . '/demo' );

// Get a value from the config
$old_host = $config->get('database.drivers.mysql.host');

// Change the value at the run-time
$config->set('database.drivers.mysql.host', '127.0.0.1');

// Sync the changes with the source file
$config->sync('database'); 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢这个项目，请分享你的爱，留下一个开始，这将对我有很大的帮助。建议和贡献也受到欢迎。

## 背故事

在我的全栈开发旅程中的某个地方，我决定参与开源行业，越来越多地实践我的知识，特别是前端部分，因为它随着许多过度设计的库和框架而发展得如此之快。所以我开始计划，然后创建一个我思考了一段时间的项目。这是一个专门用于中小型项目数据库工具包。嗯，这完全值得，因为我学到了很多新东西。从比较和测试:Vue，Angular 和 React，后来我放弃了它们，到创建自己的解决方案或小软件包，如:模板，数据绑定等。我不认为我在这种情况下重新发明了轮子，因为正如我之前提到的，有很多过度设计的解决方案旨在解决所有问题并涵盖所有用例。我是一个简单、务实的人，我只想使用对我和我的产品足够的东西。用大量未使用的代码或通用解决方案来轰炸它违背了我最优先考虑的目标(对于工具包来说)，即“性能”。

说到这里，我们来到了这篇文章的主题，我遇到了一个问题，我需要配置数据库凭证的读写。我没有其他选择，我寻找一个简单的软件包，提供两种方式的配置能力，但没有办法。下面是我的用例:我正在构建一个 SQL 控制台，它一个接一个地执行命令，当开发人员键入`use _database_`时，它应该获取数据库名称并在配置文件中更新它，然后向控制台界面返回一个成功消息(当然使用 AJAX)。因此，当他继续向控制台抛出 SQL 命令时，后端代码使用新的数据库名称。这对于将来的使用将变得持久。在这种情况下，我无法使用数据库进行动态配置。为了解决我的问题，我分叉了一个现有的包，并添加了写功能，以便能够继续在控制台上工作。我使用作曲家 VCS 库来加载 fork。后来，在我完成控制台后，我创建了这个包，并在我的工具包中实现了它，因为我对那个叉子还不够熟悉。

控制台(工具包的一部分)已经准备好预发布，我将在创建主页后尽快发布。当我发布这个工具包的时候，我会发布更多关于我的旅程的细节。

就我所能回忆起的，这些是我到目前为止学到的东西(有些我听说过但从未使用过，有些我知道但我练习得更多)

*   Javascript: React，Vue，Reveal Pattern，Sub-pub，数据绑定，ES2016，Gulp，更多 VanillaJS，HTML 标签的模板化(使用自定义属性)...
*   CSS: SMACSS，更多 flexbox
*   PHP:单元测试，持续集成，超薄微框架，代码嗅探器，更多作曲家
*   其他:Git、Vim、无损耦合组件、通用模块化编程、更多命令行...

再说一次，我对我到目前为止所经历的感到非常高兴和满意。继续并开始创建您的开源项目。

> 熟能生巧

我想听听你的想法，和平！