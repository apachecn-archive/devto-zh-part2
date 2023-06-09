# Phalcon PHP 框架的电池

> 原文：<https://dev.to/adhocore/batteries-for-phalcon-framework--15hp>

所以你使用 phalconphp，喜欢它的速度和简单性，但是错过了一些很酷的东西和做事情的简洁方法？我也是。所以我最终为 phalcon 写了一个简单的即插即用的包装器，它基本上做了很多你希望已经在 phalcon 中的事情。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[ad hoc ore](https://github.com/adhocore)/[phalcon-ext](https://github.com/adhocore/phalcon-ext)

### Phalcon 的基础、适配器、扩展、中间件和实用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

## adhocore/phalcon-ext

有用的 phalcon 适配器，中间件，扩展和实用工具！

[![Travis Build](img/6f5316976636bd61b7f04c7d015f4b7b.png)](https://travis-ci.com/adhocore/phalcon-ext?branch=master)[![Latest Version](img/90c8c57f55273cf0020de0716a62b6aa.png)](https://github.com/adhocore/phalcon-ext/releases)[![Scrutinizer CI](img/b3bbb624a89cf4d8da9f9a5e3fa3bf82.png)](https://scrutinizer-ci.com/g/adhocore/phalcon-ext/?branch=master)[![Codecov branch](img/2898549e2b0ee910a9cb1e3327231bfd.png)](https://codecov.io/gh/adhocore/phalcon-ext)[![StyleCI](img/7f1686d1d31c3319603249f0cc62bb9c.png)](https://styleci.io/repos/136166947)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/adhocore/phalcon-extLICENSE)[![Donate 15](img/b2c2f94625d612f1d6f170d10bb8de6d.png)](https://www.paypal.me/ji10/15usd)[![Donate 25](img/55169c512eb5e340a3267ace0ef15ff2.png)](https://www.paypal.me/ji10/25usd)[![Donate 50](img/f0d5a67bfcb273a3a2b8548db2a580d6.png)](https://www.paypal.me/ji10/50usd)[![Tweet](img/3aedcf72e57e3736cd6ea05d11dfd7db.png)](https://twitter.com/intent/tweet?text=Adapters+middlewares+extensions+and+utilities+missing+in+phalcon+framework&url=https://github.com/adhocore/phalcon-ext&hashtags=php,phalcon,extension,middleware)

## 装置

```
composer require adhocore/phalcon-ext
```

Enter fullscreen mode Exit fullscreen mode

### 包括什么

**缓存**

*   [再说一遍](https://github.com/adhocore/phalcon-ext#cacheredis)

Cli

*   [分机](https://github.com/adhocore/phalcon-ext#cliextension)
*   [中间件](https://github.com/adhocore/phalcon-ext#climiddlewaretrait)
*   [调度](https://github.com/adhocore/phalcon-ext#clitaskscheduletask)

**分贝**

*   [分机](https://github.com/adhocore/phalcon-ext#dbextension)
*   [记录器](https://github.com/adhocore/phalcon-ext#dblogger)

**迪**

*   [分机](https://github.com/adhocore/phalcon-ext#diextension)
*   [供应商](https://github.com/adhocore/phalcon-ext#diprovidesdi)

**Http**

*   [基础中间件](https://github.com/adhocore/phalcon-ext#httpbasemiddleware)
*   [ApiAuth 中间件](https://github.com/adhocore/phalcon-ext#httpmiddlewareapiauth)
*   [缓存中间件](https://github.com/adhocore/phalcon-ext#httpmiddlewarecache)
*   [节流中间件](https://github.com/adhocore/phalcon-ext#httpmiddlewarethrottle)

**记录器**

*   [回声记录器](https://github.com/adhocore/phalcon-ext#loggerechologger)
*   [文件记录](https://github.com/adhocore/phalcon-ext#loggerlogstofile)

**邮件**

*   [邮件程序](https://github.com/adhocore/phalcon-ext#mailmailer)
*   [邮件](https://github.com/adhocore/phalcon-ext#mailmail)
*   [可邮寄](https://github.com/adhocore/phalcon-ext#mailmailable)
*   [记录器](https://github.com/adhocore/phalcon-ext#maillogger)

**效用**

*   [Opcache 引物](https://github.com/adhocore/phalcon-ext#utilopcacheprimer)

**验证**

*   [验证包装器](https://github.com/adhocore/phalcon-ext#validationvalidation)
*   [数据库存在验证器](https://github.com/adhocore/phalcon-ext#validationexistence)

**视图**

*   [树枝](https://github.com/adhocore/phalcon-ext#viewtwig)

* * *

### 快取记忆体。再说一遍

扩展`Phalcon\Cache\Backend\Redis`以允许通过底层 redis 绑定进行访问。

#### 设置

```
$di->setShared('redis', function () {
    return new \PhalconExt\Cache\Redis(new \Phalcon\Cache\Frontend\None(['lifetime' => 0]));
});

// Call native \Redis methods like:
$di->get('redis')->getConnection()->hGet();
$di->get('redis')->getConnection()->info();
```

Enter fullscreen mode Exit fullscreen mode

* * *

### Cli。延长

一定要检查一下它在[ad hoc core/CLI](https://github.com/adhocore/cli)中是如何工作的，以及它是如何集成到 [example/cli](https://github.com/adhocore/phalcon-ext./example/cli) 、 [example/MainTask.php](https://github.com/adhocore/phalcon-ext./example/MainTask.php) 中使用的&

#### 设置

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/adhocore/phalcon-ext)

用这个多功能库点燃你的 phalcon 应用程序。它是一个扩展/适配器/中间件和实用程序的集合，使得使用 phalcon 进行开发变得有趣、优雅和容易。你还可以像专业人士一样构建酷炫的控制台界面和任务调度:)

## 包含哪些内容

*   强大的动态 di 旋变器
*   通用中间件(缓存、授权、cors、节流...)用于 web 和控制台
*   一个架构良好的中间件模式，适用于 micro+mvc 应用程序
*   额外的日志记录工具
*   swiftmailer 之上的邮件程序
*   易于使用的验证:和我们以前在 laravel 中做的一样，并且很喜欢它
*   细树枝桥，这样你就可以在伏特旁边使用树枝模板
*   用于 cli 和任务调度程序的简洁、强大的 argv 解析器
*   jwt auth for APIs(作为中间件),如果你喜欢的话
*   完全控制跟踪的查询记录器

此外，它已经完全覆盖了许多用例的测试用例、完整的文档和说明性的例子。更不用说它优雅地协调了在 mvc 或 micro 模式下做事的公平性，为您提供了一种一致的通用方法。

审查者得分:10/10

感谢阅读，请试用并给予反馈。任何贡献都将受到感谢。

黑客快乐。:)

/Ahc