# 在 Heroku 上设置 Laravel 队列工作器

> 原文：<https://dev.to/postsrc/setting-up-laravel-queue-worker-on-heroku-4m00>

在 Heroku 上设置 Laravel 队列工作器非常简单。您所要做的就是在位于项目目录根级别的 Procfile 上定义队列配置，并启动一个新的 Herokku dyno 实例。

要定义队列，您必须首先将队列命名为`sqs: php artisan queue:work --timeout=1800`(可以是您想要的任何名称)，然后继续使用`php artisan queue:work`命令来确保它正在运行。

您还可以根据需要传递 queue:work 命令提供的任何标志。下一步您必须做的是将 Laravel 应用程序部署到 Heroku，以便您的在线应用程序得到更新。

接下来，您必须从命令行运行`heroku ps:scale web=1 sqs=1 --app your-heroku-app`，这将确保您的 Laravel 应用程序有另一个名为 sqs 的 dyno。一旦你完成了，Heroku 就可以处理你的 Laravel 队列了。

文章最初写于: [PostSrc(我的个人网站)](https://postsrc.com)