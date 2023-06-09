# 共享主机上的 Laravel 队列处理

> 原文：<https://dev.to/orobogenius/laravel-queue-processing-on-shared-hosting-1p9h>

#### 在共享服务器上设置 Laravel 队列处理的指南

[![](img/9171fb9bca1bd4121653e3672ec0f4dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nerD7orJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao_VTrskPWb_7VCP0JUNf2w.jpeg) 

<figcaption>照片由[Á·阿尔瓦罗·塞拉诺](https://unsplash.com/photos/YsvAZFxDZ_8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/search/photos/efficiency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

已经有很多关于如何在共享主机上设置 laravel 队列的指南，所以你可能已经看过或者已经有了在共享主机上使用队列的方法。如果你有，这个指南遵循同样的思路，但有一个更个性化的观点。

对于那些看到“Laravel”和“共享主机”这两个词出现在同一个句子中而感到不安的人来说，这可能不适合你，除非它适合你。

[![You just might find yourself there.](img/f427135dcd0a8a5ac260dd0ba5cdfee2.png)T2】](https://i.giphy.com/media/dOxMHecx24CY/giphy.gif)

### **TL；WR**

这个指南有点像一个故事，如果你懒得去遵循这篇文章的思路，你可以快速跳过所有内容，直接进入页面底部的解决方案。

### 思路

因此，我发现自己在共享主机上托管了一个 laravel 应用程序，正如我在共享服务器上托管 laravel 应用程序时注意到的许多事情一样，有效地设置队列处理可能是一件痛苦的事情。我的应用程序已经完成并准备投入生产，但我有一个问题，处理队列中的作业。我不可能在我的邮件上使用同步驱动程序或使用 sendmethod 而不是 queue 方法，因为那样的话，我的作业将被同步处理，我不必告诉你为什么这对业务不利。

我还有一个选择，设置队列处理。我很快谷歌了如何在共享主机上设置 laravel 队列，因为我尝试的所有选项都没有成功。有一些点击，这个特别的指南([在共享主机上使用 laravel 队列](https://medium.com/@sdkcodes/using-laravel-queues-on-shared-hosting-a-simple-guide-9f976ccf99f3))看起来很有希望，所以我决定实现它。但是有一个问题，指南指示添加 laravel scheduler 作为每分钟运行的 cron 条目。这对我来说是一个死胡同，我的共享主机提供商限制 cron 作业每 15 分钟运行一次。哎哟！如果您运行 laravel 调度程序，而不是每分钟运行一次，您会得到“没有调度的命令准备运行”的消息。

该解决方案的另一个问题是，它会不断在内存中添加新的队列工作器，队列工作器是长寿命的进程，随着时间的推移，它们会开始堆积并堵塞您的内存。当我决定稍微修改一下上面的解决方案时，我注意到了这一点。我刚刚添加了一个每 15 分钟运行一次的 cron 条目😏处理我的队列:

```
\*/15 \* \* \* \* \* /path/to/php /path/to/artisan queue:work --tries=3 
```

嗯，真令人失望，就在我以为我有了突破的时候。虽然我不打算放弃，但是任何事情都比运行同步队列处理要好。我的下一个努力是得到[主管](http://supervisord.org/)。在共享主机上设置管理员并不容易，我需要 root 权限，但我没有。我四处看了看，发现了一个很棒的关于共享主机的[设置主管指南。这个过程实质上是让您将 supervisor 安装到一个目录中，在这个目录中，您有完全的访问权限，如果您愿意的话，这是您的权限范围。您可以在项目目录中安装 supervisor，并从那里运行它。](https://medium.com/@RealPete01/setting-up-supervisor-on-a-shared-hosting-without-root-access-1d564e535d08)

最后，我终于自由了，我终于可以配置 supervisor，让它处理我的队列工作程序的重启。哦，等等，我的共享服务器没有 **easy_install** python 模块，运行 python setup.py install 命令也不起作用，因为我认为我已经逃脱了所说的权限问题。不过，我并没有从该指南中获益，这是在共享服务器上安装 supervisor 的一个很好的方法，它还让我了解了如何将软件包安装到拥有完全访问权限的目录中(另一个冒险)。试试看，它可能对你有用。

好吧，所以在我偶然发现 Laravel 的队列提供的 **— once** 选项之前，我已经用尽了选项:工作命令[见文档](https://laravel.com/docs/5.6/queues#running-the-queue-worker)。我很快就接受了它，是的，它起作用了，我只需将以下内容添加到我的 crontab 中，我的作业就能得到处理，不会让工作人员阻塞我宝贵的内存:

```
\*/15 \* \* \* \* \* /path/to/php /path/to/artisan queue:work --tries=3 --once 
```

现在我的任务正在被处理，除了**——once**选项一次只处理一个任务。真扫兴。通常，您会希望队列工作器处理队列中所有可用的作业，这样就不会有作业长时间闲置。比方说，奇迹发生了，你的队列中有 1000 个作业，是的，我知道，如果你在 15 分钟内队列中有 1000 个作业，你在共享服务器上托管做什么？迁就我一下，说你得到了一千份工作。 **— once** 选项每 15 分钟只处理一个任务，即每小时 4 个任务，每 10 天 1000 个任务。想象一下，注册一项服务，10 天后收到一封欢迎邮件。

[![Awkward!](img/dc6f9f7a120d0ec7ad780005d9f9b321.png)T2】](https://i.giphy.com/media/gOmfNDR45WAb6/giphy.gif)

这促使我寻找一种解决方案，让我在每次队列工作器运行时运行我的所有作业，不会让工作器进程运行，这样它们就会开始堆积在内存中，并让我晚上睡个好觉。最后一部分对我来说很重要。不保持工作进程运行意味着我必须找到一种方法不在守护模式下运行队列工作进程。据我所知，laravel 没有提供那个选项，要么你用**——once**选项运行它，要么它以守护模式运行，就像一个二进制独裁者。

由于 laravel 的可扩展性，我很快就编写了一个控制台命令，现在我已经将它打包并通过 composer([queue worker/sans daemon](https://packagist.org/packages/queueworker/sansdaemon))提供，它扩展了原始的 queue:work 命令。这意味着原来的 laravel 命令仍然有效，但是有了我非常渴望的额外选项。使用这个命令，我可以将队列工作器添加到 cron 并让它运行。它将运行我的所有作业并退出工作进程，而不是以守护模式运行。该命令还提供了一个可选的 **—作业**选项，该选项指定了每次队列工作器运行时希望运行的作业数量。

```
\*/15 \* \* \* \* \* /path/to/php /path/to/artisan queue:work --sansdaemon --tries=3 
```

该软件包可在 GitHubT3 上获得，那里也提供了如何使用它的说明。可以的话留下一颗星。😉

PS:尽量避免在共享服务器上托管 laravel 应用，相信我，你晚上会睡得更好。

#### TL；速度三角形定位法(dead reckoning)

> 下面提供了在共享服务器上处理 laravel 队列的工作解决方案。
> 
> 1.  运行 laravel 调度程序，每分钟处理您的队列。([在共享主机上使用 laravel 队列](https://medium.com/@sdkcodes/using-laravel-queues-on-shared-hosting-a-simple-guide-9f976ccf99f3)
>     
>     
> 2.  [在共享主机上设置管理器](https://medium.com/@RealPete01/setting-up-supervisor-on-a-shared-hosting-without-root-access-1d564e535d08)和[配置](https://laravel.com/docs/5.6/queues#supervisor-configuration)来管理 laravel 队列工人。
>     
>     
> 3.  安装这个 laravel [**包**](https://github.com/orobogenius/sansdaemon) ，它扩展了 laravel 队列工作器，并提供了一种在不将工作器置于守护模式的情况下运行队列的方法。安装完软件包后，只需将下面的 cron 作业添加到 cron 选项卡中。
>     
>     
> 
> [cron schedule]/path/to/PHP/path/to/artisan 队列:work - sansdaemon - tries=3