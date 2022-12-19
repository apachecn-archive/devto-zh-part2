# 无需外部工具即可清理未使用的 Docker 数据

> 原文：<https://dev.to/mayeu/cleaning-unused-docker-data-without-external-tools-e24>

警告:在输入你可能会后悔的命令之前，请阅读全文。

如果您是 docker 用户(可能是在生产环境中，也可能是在您的开发机器上)，您可能已经积累了相当多现在无用的数据。有一些现有的工具来清理你的守护进程中未使用的图像和容器(如 [docker-gc](https://github.com/spotify/docker-gc) )，但由于 [docker API 1.25](https://docs.docker.com/engine/api/v1.25/) docker 有一个易于使用的`prune`命令，对你来说已经足够好了。

基本用法很简单:

```
$ sudo docker system prune
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache
Are you sure you want to continue? [y/N] y

Deleted Containers:
deleted: sha256:ea43728b2d10e7b0fe24036f9531caac96bd02f779b95a6620110f00ccd3b002
deleted: sha256:022db612b3070971ce7d51778806a1f995a9c3aa1a741a6c0be0bca603787387
... approximately 2 gajillion hashes later...

Total reclaimed space: 5.64GB 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但它不能清洁所有不用的东西。为此，你应该添加`--all`选项来移除所有的图片，而不仅仅是悬挂的图片:

```
$ sudo docker system prune --all
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all images without at least one container associated to them
        - all build cache
Are you sure you want to continue? [y/N] y
... moar hashes...

Total reclaimed space: 26.78GB 
```

Enter fullscreen mode Exit fullscreen mode

当然，这可能并不理想。毕竟拥有缓存是非常有用的。只有一个选项:`--filter`(仅从 [API 1.28](https://docs.docker.com/engine/api/v1.28/) 开始提供)。在我写作的时候，只有两个过滤器:`until`和`label`。在我的例子中，我只使用了`until`，它允许你清除所有比指定时间戳更老的东西。

当然，我没有在命令中添加时间戳，我使用`date`来生成一个基于人类可读日期的时间戳:

```
$ sudo docker system prune --all --filter until=$(date -d "1 month ago" +%s) 
```

Enter fullscreen mode Exit fullscreen mode

这个命令通过`$( command )`使用替换，这允许你得到一个命令的结果，并把它作为一个字符串注入到另一个命令中。date 命令本身被分解为:`date -d <time description> <time format>`，其中`+%s`是获取时间戳的格式。你可以查看`man date`了解更多信息。

瞧😃我很少需要我的笔记本电脑上的缓存超过一个月(一个月已经很保守了，但那是因为我很少有好的互联网，所以我尽可能多的缓存)。

最后，如果你觉得足够冒险，你可以添加`--force`选项，这样命令就不会要求确认。我在持续集成服务器上的 acronjob 中使用了以下代码:

```
$ sudo docker system prune --force --all --filter until=$(date -d "1 week ago" +%s) 
```

Enter fullscreen mode Exit fullscreen mode

快乐清洁♻️

*关于`sudo`的一个注意事项:在我的开发机器(GNU/Linux 系统)上，我只通过`sudo`使用 docker，因为不这样做意味着你的用户一直拥有和 root 一样的权力。这是因为 docker 守护程序是以 root 用户身份运行的特权进程。参见官方[码头安全](https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface)页面。*

*本文最初发表于 [mayeu.me](https://mayeu.me/blog/cleaning-unused-docker-data/) 。*