# 拉维尔·诺迪

> 原文：<https://dev.to/sarfraznawaz2005/laravel-noty-3642>

Laravel 软件包将漂亮的 noty 通知作为 flash 消息合并到 laravel 中。

## 2005 年 / [noty](https://github.com/sarfraznawaz2005/noty)

### Laravel 软件包将漂亮的 noty 通知作为 flash 消息合并到 laravel 中。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Latest Version on Packagist](img/06461a41d68babb754acbf9043b1f8a1.png) ](https://packagist.org/packages/sarfraznawaz2005/noty) [ ![Total Downloads](img/2be1f8f52b9458138e92219dad021453.png)](https://packagist.org/packages/sarfraznawaz2005/noty)

# 拉维尔·诺迪

Laravel 软件包将漂亮的 noty 通知作为 flash 消息合并到 laravel 中。

*用 [noty](https://github.com/needim/noty) 的 3.2.0 版本测试。*

## 屏幕上显示程序运行的图片

[![Main Window](img/e533feb2f1b0ea94d7478dee0c6dcfea.png)T2】](https://github.com/sarfraznawaz2005/noty/blob/master/screen.jpg?raw=true)

## 要求

*   PHP >= 5.6
*   拉勒维尔 5 号
*   [注](https://github.com/needim/noty)

## 装置

通过作曲家

```
$ composer require sarfraznawaz2005/noty
```

Enter fullscreen mode Exit fullscreen mode

对于 Laravel < 5.5:

将服务提供商添加到`providers`部分的`config/app.php`

```
Sarfraznawaz2005\Noty\NotyServiceProvider::class,
```

Enter fullscreen mode Exit fullscreen mode

* * *

通过运行以下命令发布包的配置文件:

```
$ php artisan vendor:publish --provider="Sarfraznawaz2005\Noty\NotyServiceProvider"
```

Enter fullscreen mode Exit fullscreen mode

它应该发布`config/noty.php`配置文件。

## 设置注释

在使用这个包之前，确保你已经通过使用 npm/yarn/etc 或者直接包含它的 css 和 js 文件将 [noty](https://github.com/needim/noty) 库包含到你的项目中:

```
<head>
    <link rel="stylesheet" href="/noty.css"/&gt
    <script type="text/javascript" src="/noty.js"></script>
</head>
```

Enter fullscreen mode Exit fullscreen mode

然后将其添加到视图/布局文件中:

```
@include('noty::view')
```

Enter fullscreen mode Exit fullscreen mode

## 使用

语法:

```
noty(
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/sarfraznawaz2005/noty)