# 如何为 Docker 容器设置日志循环

> 原文：<https://dev.to/ykyuen/how-to-setup-log-rotation-for-docker-container-4alp>

*最初发布在[水手长博客](https://blog.boatswain.io/post/docker-container-log-rotation/)上。*

* * *

## 我们都需要日志！

有时候和 [Docker](https://www.docker.com/) 一起工作让我感觉像是在和一个黑盒一起工作，尤其是在玩来自社区的 [Docker](https://www.docker.com/) 图片的时候，它并没有按照预期的方式进行。在许多情况下，读取日志会占用调试的很大一部分时间。

[![](img/c7a954d7fc7d8c2bdca973726a398a16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0NTA-1ye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwf9nqcvpp6bp8e4av5x.jpg)

这篇文章是关于为 [Docker](https://www.docker.com/) 容器设置日志轮换的。

## 默认的日志记录驱动

我们可以为容器配置不同的日志驱动程序，默认情况下，容器的 **stdout** 和 **stderr** 被写入位于 */var/lib/docker/containers/【容器标识】/【容器标识】-json.log* 的 json 文件中。如果您让它无人看管，它可能会占用大量的磁盘空间，如下所示。

[![A large log file in json format](img/a2c389ab594b07b339050d9166e477a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5vV0Aro0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ewyzkc6d6pjiorzwaszz.jpg)

### 手动清除日志

如果这个 json 日志文件占用了大量磁盘空间，我们可以使用下面的命令清除它。

```
truncate -s 0 <logfile> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以设置一个 cronjob 来定期清除这些 json 日志文件，但是从长远来看，设置日志轮换会更好。

## 设置日志旋转

### 配置默认的日志记录驱动

这可以通过在 */etc/docker/daemon.json* 中添加以下值来完成。如果该文件不存在，请创建它。

```
{  "log-driver":  "json-file",  "log-opts":  {  "max-size":  "10m",  "max-file":  "10"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

json-file 日志驱动有更多的选项，我们甚至可以换成其他日志驱动，比如 *syslog* 。更多信息请参考 [Docker 文档-配置日志驱动](https://docs.docker.com/config/containers/logging/configure/)。

执行以下命令来重新加载更新后的 *daemon.json* 。重新启动后，新配置将应用于所有新创建的容器。

```
$ systemctl daemon-reload

$ systemctl restart docker 
```

Enter fullscreen mode Exit fullscreen mode

### 为容器配置日志驱动

如果您不想全局应用它，也可以在容器级别进行配置。

#### docker 运行命令

我们可以在 *docker run* 命令中指定日志驱动程序和选项。比如:

```
$ docker run \
    --log-driver json-file \
    --log-opt max-size=10m \
    --log-opt max-file=10 \
    alpine echo hello world 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用 docker-compose

也可以使用 docker-compose 配置日志驱动程序和选项。例如:

```
version: '3.2'
services:
  nginx:
    image: 'nginx:latest'
    ports:
      - '80:80'
    logging:
      driver: "json-file"
      options:
        max-size: "1k"
        max-file: "3" 
```

Enter fullscreen mode Exit fullscreen mode

验证设置是否正常工作。

[![The logs are broken down into 1k files](img/09779e6ae8b7e34f31b48fcc33ed0581.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bE2wId7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8rmiccbwvjvkn6a0rvh3.jpg)

## 总结

尽管默认设置很好，但您永远不知道容器日志何时会占用所有磁盘空间，这可以通过上面的几个步骤来避免。除此之外，日志是重要的资产，它不仅在出错时有用，而且包含许多隐藏的价值。所以永远不要让原木离开。

如果你正在寻找一个**日志管理 SAAS 解决方案**，考虑使用[水手长](https://boatswain.io/)，我们会帮助你管理所有的日志以及监控你的 [Docker](https://www.docker.com/) 服务器。💫

[![Insufficient facts always invite danger](img/1f44622856f03aaaf0489fe420fc32b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lVE4ZvxZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bq2s1t4uivetgtremwxg.jpg)

> 事实不足总是招致危险。
> -斯波克@星际迷航