# 如何在 Laravel 中设置作业队列？

> 原文：<https://dev.to/upnrunnhq/how-to-set-up-job-queue-in-laravel-i4k>

今天我将展示如何在 **Laravel 5.6** 上设置作业队列，所以首先让我解释一下什么是作业队列？

"队列允许您将耗时的任务(如发送电子邮件)推迟到以后处理。推迟这些耗时的任务会大大加快对应用程序的 web 请求。”

作业队列是这样设置的，laravel 系统必须在后台运行服务器上的进程，而不停止/中断当前的执行。它有助于运行复杂耗时的任务。

以便进程或代码将在指定站点的服务器端运行。所以这将提高网站速度。

让我们说，你有一个网站，它有新用户注册用户的邮件。而一旦用户注册了站点，邮件就会被发送到他们的地址。

这将需要很少的时间，而用户注册网站的邮件将需要 4-5 秒发送一封电子邮件。

因此，用户将体验到网站的缓慢，为了解决这个问题，可以使用 laravel 作业队列，比如说，一旦用户在网站上注册成功，就会创建一个新的作业流程，它将在后台执行，而不会影响当前的执行。

设置作业队列包括几个步骤。

**第一步:队列配置**

队列配置文件存储在 **config/queue.php** 中。在这个文件中，您将找到框架中包含的每个队列驱动程序的连接配置，其中包括一个**数据库**、 **Beanstalkd** 、**亚马逊 SQS** 、 **Redis** ，以及一个将立即执行作业的**同步**驱动程序(供本地使用)。还包括一个空队列驱动程序，它可以丢弃排队的作业。

在这里，我提供数据库作为队列驱动程序设置选项。

英寸环境文件添加/编辑以下值

`QUEUE_DRIVER=database`

因为它在 config/queue.php 文件中有 sync as 默认值。

**注意**:还有许多其他驱动程序可供使用，更多参考见以下文档:

**https://laravel.com/docs/5.6/queues**

**步骤 2:为队列**设置数据库表

因此，运行以下命令来创建队列表模式迁移。

`“php artisan queue:table”`

然后运行`“php artisan migrate”`运行队列表的迁移。

**步骤 3:创建工作**

要在那里创建作业，您可以运行`“php artisan make:job ProcessPodcast”`，这将在‘app \ Jobs’目录下创建一个新的作业文件。

它将包含以下文件内容

当队列处理作业时，调用**‘handle’**方法。请注意，我们能够在作业的 handle 方法上键入提示依赖关系。Laravel 服务容器自动注入这些依赖项。

**步骤 4:分派作业**

一旦你写了你的作业类，你可以在作业上使用' **dispatch** '方法来调度它。传递给 dispatch 方法的参数将被提供给作业的构造函数:

从您的控制器文件中，您可以通过添加

**第五步:流程队列**

要处理创建的作业队列，需要运行以下命令

`php artisan queue:work --tries=3`

一种指定工作最大尝试次数的方法是通过 Artisan 命令行上的`--tries`开关:

您也可以指定尝试作业文件

`public $tries = 5;`像这样。

一旦编写了作业类，就可以在作业本身上使用**‘dispatch’**方法来调度它。传递给 dispatch 方法的参数将被提供给作业的构造函数。

**步骤 5 的替代:设置监控器**

如果您有 live 服务器，那么建议您在服务器上设置 supervisor，它会自动运行队列工作器。

要配置主管，包括以下步骤

1.  **i)** 安装主管

    运行`“sudo apt-get install supervisor”`命令。

2.  **ii)** 配置主管

管理员配置文件通常存储在 **/etc/supervisor/conf.d** 目录中。在这个目录中，您可以创建任意数量的配置文件，指导 supervisor 如何监控您的流程。例如，让我们创建一个 **laravel-worker.conf** 文件，该文件启动并监控一个**队列:工作**进程:

在这个例子中，numprocs 指令将指示 Supervisor 运行 8 个 queue:work 进程，并监控所有这些进程，如果它们失败，将自动重新启动它们。当然，您应该更改命令指令的 queue:work sqs 部分，以反映您想要的队列连接。

**iii)** 启动主管

创建配置文件后，您可以使用以下命令更新 Supervisor 配置并启动进程:

`sudo supervisorctl reread`

`sudo supervisorctl update`

`sudo supervisorctl start laravel-worker:*`

有关主管的更多信息，请查阅[主管文件。](http://supervisord.org/index.html)

**步骤 6:处理失败的作业**

有时，您的排队作业会失败。别担心，事情并不总是按计划进行的！Laravel 提供了一种便捷的方式来指定一个作业应该尝试的最大次数。在作业超过这个尝试次数后，它将被插入到 **failed_jobs** 数据库表中。要为 failed_jobs 表创建迁移，可以使用**队列:失败表**命令:

`php artisan queue:failed-table`

`php artisan migrate`

然后，当运行您的队列工作器时，您应该使用 queue:work 命令上的**–tries**开关来指定作业应该尝试的最大次数。如果不指定**–尝试**选项的值，将无限期尝试作业:

`php artisan queue:work redis --tries=3`

作业队列在系统中非常有用，例如，如果您在一台服务器上有大量数据，并希望使用 API 将数据传输到另一台服务器，那么强烈建议您使用作业队列。

如何在 Laravel 中设置作业队列？最早出现在 [upnrunn | Build &部署强大的应用](https://upnrunn.com)。