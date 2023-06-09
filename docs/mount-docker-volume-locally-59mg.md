# 本地装载 Docker 卷

> 原文：<https://dev.to/adamkdean/mount-docker-volume-locally-59mg>

编辑 2021 年 10 月 25 日:似乎这不再管用。这就是解决办法。

这是很酷的东西。

您可以拥有一个 docker 卷`docker volume create example`，并且可以将该卷挂载到一个容器`docker run -v example:/path image`。您还可以将本地目录挂载到容器中，`docker run -v $(pwd)/local:/path image`。但是您不能将本地目录挂载到卷上。

场景:您有一个卷，您有一个使用该卷的容器，并且您有一些需要在 docker 之外本地访问该卷数据的软件。

🚫`docker run -v $(pwd)/local:example image`没用。

你也不能在同一个地方安装两个东西。

🚫也不会起作用。

解决方案是使用中间符号链接容器。

我称这个解决方案为地图体积。

```
#
# map volume
#
FROM busybox
RUN mkdir /volume
RUN ln -s /local /volume
CMD tail -f /dev/null 
```

Enter fullscreen mode Exit fullscreen mode

在这个 docker 文件中，您可以看到我们创建了一个目录(稍后将用作挂载点)，然后我们创建了一个指向相同位置的 symlink 目录。

```
$ docker build -t mapvolume .
$ docker volume create my_precious_data
$ docker run -d -v my_precious_data:/volume -v $(pwd)/my_precious_data:/local mapvolume 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个卷`my_precious_data`，然后运行 mapvolume，首先将卷`my_precious_data`挂载到容器路径`/volume`，然后将本地目录`$(pwd)/my_precious_data`挂载到容器路径`/local`，正如我们看到的，它实际上是`/volume`。

现在，您已经将 docker 卷挂载到了本地文件系统中。

**更新:**我今天意识到原来的 dockerfile 文件不会继续存在了。因此，我将 CMD 改为`CMD tail -f /dev/null`,以保持容器活动。感谢[bigdatums.net](http://bigdatums.net/2017/11/07/how-to-keep-docker-containers-running/)带来的片段。