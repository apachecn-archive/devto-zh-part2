# Laravel 备份管理员

> 原文：<https://dev.to/sarfraznawaz2005/laravel-backupmanager-4fhm>

简单的 laravel 包来备份/恢复文件和数据库。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[sarfraznawaz 2005](https://github.com/sarfraznawaz2005)/[备份经理](https://github.com/sarfraznawaz2005/backupmanager)

### 简单的 laravel 包来备份/恢复文件和数据库。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Latest Version on Packagist](../Images/e6327a1b93f86a38dab83054cea7e9b9.png) ](https://packagist.org/packages/sarfraznawaz2005/backupmanager) [ ![Total Downloads](../Images/81ea39c37edb8ee03834493a44bd31ec.png)](https://packagist.org/packages/sarfraznawaz2005/backupmanager)

# Laravel 备份管理员

简单的 laravel 包来备份/恢复文件和数据库。

## 屏幕上显示程序运行的图片

[![Main Window](../Images/a1fae3e868cd35191c501ab8a683f516.png)T2】](https://github.com/sarfraznawaz2005/backupmanager/blob/master/screen.gif?raw=true)

## 要求

*   PHP >= 5.6
*   拉勒维尔 5 号
*   `mysql`(恢复数据库)
*   `mysqldump`(备份数据库)
*   `tar`(备份/恢复文件)
*   `zcat`(提取数据库存档)

请确保将上述二进制文件添加到`PATH`环境变量中，或者您可以在配置文件中指定它们的完整路径。

## 装置

通过作曲家

```
$ composer require sarfraznawaz2005/backupmanager
```

Enter fullscreen mode Exit fullscreen mode

对于 Laravel < 5.5:

将服务提供商添加到`providers`部分的`config/app.php`:

```
Sarfraznawaz2005\BackupManager\ServiceProvider::class,
```

Enter fullscreen mode Exit fullscreen mode

(可选)将外观添加到`aliases`部分的`config/app.php`:

```
'BackupManager' => Sarfraznawaz2005\BackupManager\Facades\BackupManager::class,
```

Enter fullscreen mode Exit fullscreen mode

* * *

通过运行以下命令发布包的文件:

```
$ php artisan vendor:publish --provider="Sarfraznawaz2005\BackupManager\ServiceProvider"
```

Enter fullscreen mode Exit fullscreen mode

它应该发布`config/backupmanager.php.php`配置文件和迁移文件。

运行`php artisan migrate`创建备份验证器(`verifybackup`))表。

* * *

最后在`config/backupmanager.php`文件中设置选项，并打开备份管理器

</article>

[View on GitHub](https://github.com/sarfraznawaz2005/backupmanager)