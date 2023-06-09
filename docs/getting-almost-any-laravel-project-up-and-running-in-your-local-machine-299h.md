# 在本地机器上启动并运行几乎所有的 Laravel 项目

> 原文：<https://dev.to/m1guelpf/getting-almost-any-laravel-project-up-and-running-in-your-local-machine-299h>

在用三个不同的文档页面写完这个指南后，我决定将它抽象成一篇我可以随时更新的文章。请记住，这是一般说明，您可能需要根据您要运行的内容遵循一些自定义步骤。也就是说，你需要做的是:

## 要求

在开始之前，这里有一些你需要的东西:

*   PHP 7——学习如何在 [Windows](https://www.jeffgeerling.com/blog/2018/installing-php-7-and-composer-on-windows-10) 、 [Mac](https://medium.com/@romaninsh/install-php-7-2-xdebug-on-macos-high-sierra-with-homebrew-july-2018-d7968fe7e8b8) 或 [Linux](https://tecadmin.net/install-php-7-on-ubuntu/) 中安装它
*   Composer -学习[如何安装](https://getcomposer.org/doc/00-intro.md)
*   终端——你的操作系统应该已经有了

## 把代码输入你的电脑

让我们假设项目托管在 GitHub 上(或者 GitLab，任何 git 服务都应该可以)。首选方法是执行 git 克隆。您可以通过在您选择的终端中运行以下命令来做到这一点:

```
git clone https://github.com/someuser/someproject 
```

Enter fullscreen mode Exit fullscreen mode

当然，并不是每个人都有或者想要使用 git，所以几乎所有的 git 服务都提供了一种下载资源库压缩版本的方法。只需搜索“*下载 ZIP* ”选项。然后，在进行下一步之前，您需要解压缩它。

## 安装依赖项

为了扩大存储库的规模，Laravel 项目很少将它们的依赖项捆绑在一起。你需要使用`composer`来下载它们。为此，只需在存储库文件夹上打开一个终端窗口并运行以下命令:

```
composer install 
```

Enter fullscreen mode Exit fullscreen mode

根据依赖项的数量，此过程可能需要一至五分钟。

## 设置数据库

大多数 Laravel 应用程序需要一个数据库。为了简单起见，我们将使用一个 sqlite 数据库，它只是一个简单的文件，不需要外部程序。要配置它，在存储库上搜索一个`.env.example`文件，并将其重命名为`.env`。你还需要打开`database`文件夹，创建一个名为`database.sqlite`的空文件。在这之后，在文本编辑器中打开你的`.env`文件并做如下修改:

```
-DB_DRIVER=mysql
-DB_DATABASE=homestead +DB_DRIVER=sqlite
+DB_DATABASE=database/database.sqlite 
```

Enter fullscreen mode Exit fullscreen mode

最后，打开一个终端窗口并运行以下命令:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

## 设置应用密钥

简单一点！只需打开一个终端窗口，运行以下命令，就大功告成了！

```
php artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode

## 让服务器运行

你几乎可以使用任何服务器来服务你的应用，但是为了简单起见，我们将使用 Laravel 的集成服务器。打开终端窗口，运行以下命令:

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该可以通过[使用此链接](http://localhost:8080)访问您的应用程序。

## 就是这样！

如果一切顺利，您现在应该可以看到 Laravel 项目的主页了。恭喜你，享受 Laravel 的力量！

*喜欢这篇文章？考虑[支持我的工作](https://miguelpiedrafita.com/patreon)。*