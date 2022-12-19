# Laravel 电子邮件观察

> 原文：<https://dev.to/sarfraznawaz2005/laravel-emailwatch-3o75>

我在寻找一个包，可以在默认的电子邮件客户端自动打开发送的电子邮件，以便在测试和开发应用程序时变得更容易。所以我造了一个。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[2005 年](https://github.com/sarfraznawaz2005) / [电子邮件观察](https://github.com/sarfraznawaz2005/emailwatch)

### Laravel 软件包直接在默认邮件客户端打开发送的邮件。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Latest Version on Packagist](../Images/a9107ef3f70c7a4c1e01fc213d9476a7.png) ](https://packagist.org/packages/sarfraznawaz2005/emailwatch) [ ![Total Downloads](../Images/b22b701eb8e60ddbd4c7e7380cd06bfb.png)](https://packagist.org/packages/sarfraznawaz2005/emailwatch)

# Laravel 电子邮件观察

Laravel 包可以在发送电子邮件时直接在**默认电子邮件客户端**打开发送的电子邮件，这对本地测试非常有用。

**注意:**您需要将默认邮件客户端设置为默认打开 ***eml*** 格式文件，否则无法使用。通常大多数电子邮件客户端都是打开的。默认为 eml 文件。

## 要求

*   PHP >= 5.6
*   Laravel 5.5 或更高版本

## 装置

通过作曲家

```
$ composer require sarfraznawaz2005/emailwatch
```

* * *

通过运行以下命令发布包的配置文件:

```
$ php artisan vendor:publish --provider="Sarfraznawaz2005\EmailWatch\ServiceProvider"
```

它应该发布`config/emailwatch.php`配置文件。现在，通过设置`config/emailwatch.php`中的`enabled`值来确保包被启用。

## 信用

*   萨拉兹·艾哈迈德
*   [所有贡献者](https://github.com/sarfraznawaz2005/emailwatch/graphs/contributors)

## 许可证

更多信息请参见[许可文件](https://raw.githubusercontent.com/sarfraznawaz2005/emailwatch/master/readme.md/license.md)。

</article>

[View on GitHub](https://github.com/sarfraznawaz2005/emailwatch)