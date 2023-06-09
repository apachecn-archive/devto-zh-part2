# 如何配置新的 Laravel 项目(使用 Tailwind CSS)

> 原文：<https://dev.to/chhedamilan/how-to-configure-a-new-laravel-project-with-tailwind-css-l1h>

最近，我一直在做几个 laravel 项目，每次我配置新项目时，我都会因错过/忘记一些重要步骤而犯下严重错误。毫无疑问， [Laravel 文档](https://laravel.com/docs/5.5/installation)非常出色，在安装/设置 Laravel 方面做得很好，但是在我们真正开始工作之前，还有一些事情需要配置。所以我想出了这个清单，作为一个现成的计算器来设置和配置一个新的 laravel 项目。

通过运行以下命令，使用 [laravel 安装程序](https://laravel.com/docs/5.5/installation#installing-laravel)创建一个新的 laravel 项目:

```
composer create-project --prefer-dist laravel/laravel <project-name> "5.5.*" 
```

从命令行创建 MySQL 数据库:

```
mysql -u<mysql-username> -p create database <project-database-name>; 
```

Laravel 为我们提供了开箱即用的。来配置我们的项目。让我们用上面的数据库凭证更新该文件。

```
DB_DATABASE=<project-database-name>

DB_USERNAME=<mysql-username>

DB_PASSWORD=<mysql-password> 
```

在我 99%的项目中，我使用开箱即用的身份验证。确保您在项目根目录中，并执行下面的命令。如果不需要开箱认证，可以感觉跳过这一步。

```
php artisan make:auth 
```

一旦执行了上述步骤，您就可以运行迁移并创建身份验证表。

```
php artisan migrate 
```

通过从项目根目录运行下面的命令来安装 JS 包

```
npm install 
```

在开始开发之前，安装其他必需的依赖项。在我最近的项目中，我一直在使用 [Tailwind CSS](https://tailwindcss.com/docs/what-is-tailwind) ，因此将它作为初始依赖项添加进来。

安装 tailwindcss 包:

```
npm install tailwindcss --save-dev 
```

运行以下命令来生成配置文件

```
./node_modules/.bin/tailwind init 
```

我更喜欢使用 sass，所以用下面的内容更新 app.sass 文件(以同样的顺序):

```
@tailwind preflight
@tailwind utilities 
```

添加到 webpack.mix.js 顶部的线下

```
let tailwindcss = require('tailwindcss'); 
```

最后，用下面的代码替换 sass()代码:

```
.sass('resources/assets/sass/app.sass', 'public/css').options({ 
    processCssUrls: false, postCss:[tailwindcss(‘./tailwind.js')], 
}); 
```

耶！我们准备开始编码。从项目根目录运行下面的命令，启动 PHP 的内置服务器并开始开发。

```
php artisan serve 
```

**就这样。我希望这个清单有助于在几分钟内配置一个新的 laravel 项目，从而避免任何明显的错误，这些错误占用了实际开发的生产时间。**

编码快乐！:)

*原载于 2018 年 2 月 9 日*[*【milanchheda.com】*](https://milanchheda.com/how-to-configure-a-new-laravel-project)*。*