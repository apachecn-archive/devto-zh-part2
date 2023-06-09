# Laravel VisitLog

> 原文：<https://dev.to/sarfraznawaz2005/laravel-visitlog-1kja>

VisitLog 是一个简单的 Laravel 5 包，可以用来记录访问者信息并保存到数据库中。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[sarfraznawaz 2005](https://github.com/sarfraznawaz2005)/[访问日志](https://github.com/sarfraznawaz2005/visitlog)

### ⏰Laravel 软件包将访问者信息记录到数据库中。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Laravel VisitLog

[![Total Downloads](img/a7d679774368065607adeec11a553b73.png)T2】](https://packagist.org/packages/sarfraznawaz2005/visitlog)

## 介绍

VisitLog 是一个简单的 Laravel 5 包，可以用来记录访问者信息并保存到数据库中。

## 特征

*   除了基本日志，如 IP，浏览器和操作系统，它还可以记录位置信息。
*   允许记录基于 IP 的唯一和非唯一访问。
*   允许缓存基于 IP 的访问。
*   允许记录经过验证的用户信息。
*   提供现成的日志查看器页面。
*   为应用程序用户提供基本的 http 身份验证。
*   能够禁止用户的 IP

**注意:** VisitLog 无法检测到来自某个匿名者的相同用户/IP，因此无法区分。

## 屏幕上显示程序运行的图片

[![Main Window](img/824de96ef8d8e09c342154abff97eb50.png)T2】](https://camo.githubusercontent.com/dda1747e8bf219341ce63ffdfaaf73841117294f0f3790d4fbe82290992ba757/68747470733a2f2f7261772e6769746875622e636f6d2f7361726672617a6e6177617a323030352f76697369746c6f672f6d61737465722f73637265656e2e706e67)

*注意:上面截图中的信息是假的。*

## 要求

*   PHP >= 5.5.9
*   拉勒维尔 5 号

## 装置

通过 composer 安装

```
composer require sarfraznawaz2005/visitlog 
```

对于 Laravel < 5.5:

将服务提供商添加到`providers`部分的`config/app.php`

```
Sarfraznawaz2005\VisitLog\VisitLogServiceProvider::class,
```

Enter fullscreen mode Exit fullscreen mode

将外观添加到`aliases`部分的`config/app.php`

```
'VisitLog' => Sarfraznawaz2005
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/sarfraznawaz2005/visitlog)