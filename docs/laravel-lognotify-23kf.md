# Laravel 日志通知

> 原文：<https://dev.to/sarfraznawaz2005/laravel-lognotify-23kf>

Laravel 包自动显示通知实时每当有新的日志条目在应用程序中的任何地方。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[sarfraznawaz 2005](https://github.com/sarfraznawaz2005)/[日志通知](https://github.com/sarfraznawaz2005/lognotify)

### Laravel 包自动显示通知实时每当有新的日志条目在应用程序中的任何地方。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Latest Version on Packagist](img/a2b04f91756aa99e44abb7050db5652a.png) ](https://packagist.org/packages/sarfraznawaz2005/lognotify) [ ![Total Downloads](img/8d86571c246506be80663b642ca66708.png)](https://packagist.org/packages/sarfraznawaz2005/lognotify)

# Laravel 日志通知

Laravel 包自动显示通知实时每当有新的日志条目在应用程序中的任何地方。

## 屏幕上显示程序运行的图片

[![Main Window](img/d5d56b07e530e1d0f547ddfd649e988c.png)T2】](https://github.com/sarfraznawaz2005/lognotify/blob/master/screen.jpg?raw=true)

## 要求

*   PHP >= 5.6
*   拉勒维尔 5 号

## 装置

通过作曲家

```
$ composer require sarfraznawaz2005/lognotify
```

Enter fullscreen mode Exit fullscreen mode

对于 Laravel < 5.5:

将服务提供商添加到`providers`部分的`config/app.php`

```
Sarfraznawaz2005\LogNotify\LogNotifyServiceProvider::class,
```

Enter fullscreen mode Exit fullscreen mode

* * *

通过运行以下命令发布包的配置文件:

```
$ php artisan vendor:publish --provider="Sarfraznawaz2005\LogNotify\LogNotifyServiceProvider"
```

Enter fullscreen mode Exit fullscreen mode

它应该发布`config/lognotify.php`配置文件。

## 使用

将此添加到您的应用布局文件/页脚中:

```
@include('logNotify::view')
```

Enter fullscreen mode Exit fullscreen mode

然后通过发出以下命令启动套接字服务器:

```
$ php artisan lognotify:serve
```

Enter fullscreen mode Exit fullscreen mode

现在，无论何时在 laravel 日志文件中创建新条目，您都会在应用程序的右下角看到弹出通知。

## 自定义通知

您可以通过在`resources/views/vendor/logNotify/view.blade.php`修改发布的文件来自定义通知界面

## 信用

*   萨拉兹·艾哈迈德
*   [所有贡献者](https://github.com/sarfraznawaz2005/lognotify/graphs/contributors)

## 许可证

更多信息请参见[许可文件](https://raw.githubusercontent.com/sarfraznawaz2005/lognotify/master/readme.md/license.md)。

</article>

[View on GitHub](https://github.com/sarfraznawaz2005/lognotify)