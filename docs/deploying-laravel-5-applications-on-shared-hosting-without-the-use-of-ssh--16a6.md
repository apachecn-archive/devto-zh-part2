# 在不使用 SSH 的情况下在共享主机上部署 Laravel 5 应用程序

> 原文：<https://dev.to/usefulsomebody/deploying-laravel-5-applications-on-shared-hosting-without-the-use-of-ssh--16a6>

首先，不鼓励你的 Laravel 5 应用程序使用共享主机。还有其他非常便宜的虚拟主机，每月 7 美元起。

但是一些客户(预算非常低的客户)或者情况(例如，你想测试一些东西然后继续前进)可能会要求你使用共享主机。

最近，我不得不在一个共享主机上托管一个应用程序。我注意到互联网上有一些关于如何做到这一点的零散和不完整的文章。所以我决定想出一些完整的东西。

我将在这里介绍以下内容:

1.  如何将您的应用程序部署到共享主机。

2.  如何在不运行迁移命令的情况下迁移数据库！

第一步:

我假设你已经完成了应用程序的构建——至少是一个可以在本地主机上运行的功能性应用程序。我还假设您使用的是 Laravel 5.0——尽管这篇文章与 Laravel 5.1 相关。

假设您的 laravel 项目名为 laravel50，文件夹结构如下:

[![Your Project Structure](img/2e12595a5e4801fdcd44d7733c092be9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q8Avbn6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gkdyzo3cffk3o38xvybr.png)

注意:我建议您保持一切原样，包括。htaccess 文件。

1.  压缩本地计算机上的整个项目文件夹。您将得到一个 zip 文件——laravel 50 . zip

2.  打开您的共享托管 cPanel。
    [![cPanel Home on shared hosting](img/3e99a09cc382fd1384899fefeb707867.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--breRKGZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cezwa3tda9fwjc8ps075.png)

3.  点击“文件管理器”

4.  点击“上传”

5.  将 laravel50.zip 上传到根目录–*而不是 public_html。*

6.  解压缩 laravel50.zip。您的 cPanel 文件管理器应该类似于下面这样:

[![cPanel File manager](img/e3e54af54c404b9e023bd57824cd8af5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QfFvgtX6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/la3tti3vh7toc7fpf9fa.png)

1.  打开 laravel50 文件夹，将 public 文件夹的内容移动到 cpanel 的 public_html 文件夹中。您现在也可以删除空的公共文件夹。

2.  导航到 public_html 文件夹，找到 index.php 文件。右击它并从菜单中选择代码编辑。

3.  这将打开另一个选项卡，显示 cpanel 代码编辑器。

4.  将以下行(22 和 36)从

    ```
    `require __DIR__.'/../bootstrap/autoload.php';
     ...
     $app = require_once __DIR__.'/../bootstrap/app.php';`

     to

    `require __DIR__.'/../laravel50/bootstrap/autoload.php';
     ...
     $app = require_once __DIR__.'/../laravel50/bootstrap/app.php';` 
    ```

*注意:根据你的项目文件夹名*

1.  请不要更改您的。htaccess 文件(除非你知道你在做什么🙂)
    the。htaccess 文件应该是这样的。

    ```
     `<IfModule mod_rewrite.c>
                      <IfModule mod_negotiation.c>
                      Options -MultiViews
                      </IfModule>

                      RewriteEngine On

                    # Redirect Trailing Slashes…
                      RewriteRule ^(.*)/$ /$1 [L,R=301]

                    # Handle Front Controller…
                      RewriteCond %{REQUEST_FILENAME} !-d
                      RewriteCond %{REQUEST_FILENAME} !-f
                      RewriteRule ^ index.php [L]
                      </IfModule>` 
    ```

2.  如果一切顺利，去[http://yourdomain.com](http://yourdomain.com)应该会抛出数据库错误(如果你有模型在你的应用上运行)。不要担心！下一阶段是将数据库迁移到共享主机上。

## 迁移您的表

Laravel Framework 的一个优点是，它使您能够用一个命令`php artisan migrate`快速设置已定义的数据库。因为我们使用共享主机，所以不使用 SSH 就无法做到这一点。如果你有 10 张桌子，这很容易做到。这很简单，让我们开始吧:

在您的 web 主机上创建一个数据库。

1.  大多数 cPanel 都带有 PHPMyAdmin 和 Mysql 数据库向导。使用 Mysql 数据库向导创建一个[数据库和用户],然后将用户分配到允许所有权限的数据库。记下用户名和密码，您很快就会用到。

2.  使用 cpanel 的 PHPMyAdmin 创建您的表。为了有效地完成这项工作，请在本地机器上打开 PHPMyAdmin。对于每个表结构，在 cPanel 的 PHPMyAdmin 上创建精确的结构。
    [![This is an Example](img/75201049fc31090f0681cb8459099a11.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--LgGTmp8u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ej9k5nvva1epsyezlolo.png)

3.  还有另一种从本地机器导入和导出数据库的方法。从你的计算机登录到 PHPMyAdmin，找到你的项目使用的数据库，从工具栏中找到导出工具。

[![This is an Example](img/00f75e062f50f5cd6687bb165b369167.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--esR4IYCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tu33626wo11s1gpztc2c.png) 
导出后移至您的共享主机并做同样的操作，但这次我们使用的是导入工具

[![This is an Example](img/ed6cc2129d7e0bc67f84728545101741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l_acwaV5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ojdu1c9r4r1hkmqft6c.png) 
如果上传成功你会看到‘成功信息’

1.  在 cpanel 文件管理器上，导航到 laravel50(或您的 laravel 项目目录)。转到`config/database.php`。右键单击并选择“代码编辑”。找到第 55 行——您的 MySQL 配置部分。

将用户名和密码从 cpanel 更改为 MySQL 用户名和密码(您在前面提到过)。

确保这些详细信息输入正确。

如果一切顺利，你应该有你的网站现在工作正常。所以，去尝试一下吧。