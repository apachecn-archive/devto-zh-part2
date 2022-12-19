# 使用 Heroku 重定向到您的主站点

> 原文：<https://dev.to/larryprice/redirecting-to-your-main-site-with-heroku-4bfp>

我们有很多希望重定向到同一个服务器的域，但是我们使用的 DNS 服务不允许进行域转发，我们也不允许升级。我想尽可能用最简单的方式做到这一点，所以我使用 PHP 脚本和 Heroku 创建了一个变通办法。这篇文章中详细讨论的来源可以在 GitHub 上找到:[https://github.com/larryprice/simple-heroku-redirect-app](https://github.com/larryprice/simple-heroku-redirect-app)。

这里的目标是让用户访问一个页面，然后立即被重定向到新的网站。我已经定义了两个将在这个项目中使用的环境变量:`SITENAME`，我们网站的一个人类可读的名称，和`SITEURL`，我们实际上希望用户最终到达的完整 URL。我定义了一个名为`index.php` :
的 PHP 文件

```
<!DOCTYPE html>
<html>
  <head>
    <?php echo getenv('SITENAME') ?> - You will be redirected shortly...
    <meta http-equiv="refresh" content="0;URL='<?php echo getenv('SITEURL') ?>'" />
  </head>
  <body>
    <p>Please visit the official <?php echo getenv('SITENAME') ?> site at <a href="<?php echo getenv('SITEURL') ?>"><?php echo getenv('SITEURL') ?></a>.</p>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这里重要的部分是`<meta>`标签，它实际上为我们做了重定向。这里唯一的 PHP 代码是在模板中呈现环境变量的`echo getenv`命令。因为我是 PHP 新手，可能有更好的方法，但是`echo`工作得很好。

我们还需要告诉 Apache 如何为应用程序提供服务。我们希望匹配任何路线并呈现我们的`index.php`。所以我们创建了一个`.htcaccess`文件:

```
RewriteEngine on
RewriteRule . index.php [L] 
```

Enter fullscreen mode Exit fullscreen mode

为了让 Heroku 满意，我们需要列出 PHP 应用程序的依赖项。幸运的是，我们没有 Heroku 默认不提供的依赖项。我们将在项目的根目录下创建一个`composer.json`文件，其中包含一个空对象:

```
{} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要的一切。你可以重新创建这个项目，但是你也可以把上面列出的项目拉下来，放到 Heroku:

```
$ git clone https://github.com/larryprice/simple-heroku-redirect-app.git
$ cd simple-heroku-redirect-app
$ heroku create
$ git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

当您的应用程序在 Heroku 上可用时，我们仍然需要将前面描述的环境变量设置为[配置变量](https://devcenter.heroku.com/articles/config-vars) :

```
$ heroku config:set SITENAME=yourgooddomain.com
$ heroku config:set "SITEURL=Your Good Domain's Website Name" 
```

Enter fullscreen mode Exit fullscreen mode

现在告诉 Heroku 所有将要访问这个应用程序的域。这些是你希望用户*而不是*使用的域名:

```
$ heroku domains:add yourbaddomain.com
$ heroku domains:add www.yourbaddomain.com 
```

Enter fullscreen mode Exit fullscreen mode

现在，您只需要将上述命令指示的记录添加到您的 DNS 记录中。这些可能是从`@`指向`yourbaddomain.com.herokudns.com`或者从`www`指向`yourbaddomain.com.herokudns.com`的 CNAME 记录。