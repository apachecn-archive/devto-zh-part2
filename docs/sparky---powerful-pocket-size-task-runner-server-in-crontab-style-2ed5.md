# 强大的口袋大小的任务运行服务器在 crontab 风格

> 原文：<https://dev.to/melezhik/sparky---powerful-pocket-size-task-runner-server-in-crontab-style-2ed5>

[![Nice web UI](../Images/b7f16ec828841339f5e97da8b345a990.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2xvyz3qA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/melezhik/sparky/master/images/sparky-web-ui3.png)

有时候我需要运行一些周期性的*任务*并得到*报告*。我机器上的海峡。一些简单和可配置的东西。是的，有时我想远程运行任务**，不仅仅是在本地主机上，通过 **ssh** 或 **docker** 实例。**

 **Linux 为此提供了老一套的 cron。然而，如果任务不仅仅是小脚本，crontab 很快就会成为笨拙的解决方案，并且难以维护。有像 Jenkins 或 Team City 这样的“重型”工具，但对于“一台机器”的目的来说，这似乎有些矫枉过正。我需要轻便、快速部署和可破解的东西。

让我来介绍一下小巧灵活的克朗·乔布斯，他叫- [Sparky](https://github.com/melezhik/sparky) 。

Sparky 的基本特征:

*   以 crontab 风格定义作业时间
*   场景定义为 [Sparrowdo](https://github.com/melezhik/sparrowdo) 脚本
*   [预定义任务的漂亮集合](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md)可用
*   一切都保存在 SCM 存储库中——易于移植、维护和跟踪变更
*   任务通过 **ssh** 在 **docker** 实例上异步运行**——1)在本地主机上 2)在远程机器上**
***   提供了很好的网络用户界面来阅读任务报告**

 **感兴趣吗？我们继续吧！(:

# 安装

Sparky 是在 Perl6 上编写的，所以作为 Perl6 模块安装，我们想要最新的 GH 版本:

```
$ git clone Sparky https://github.com/melezhik/sparky.git
$ cd sparky
$ zef install . 
```

Enter fullscreen mode Exit fullscreen mode

# 初始化

Sparky 将其数据存储在数据库中，默认为`sqlite` ( mysql/postgrsql 也可以)，我们需要创建一个模式:

```
$ perl6 db-init.pl6 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们运行 sparky 服务，第一个叫做`sparky-web`的是 UI 应用程序，第二个叫做`sparkyd`的是运行任务的守护进程

最简单的方法是使用发行版附带的 Sparrowdo systemd 安装程序:

```
$ sparrowdo --sparrowfile=utils/install-sparkyd-systemd.pl6 --local_mode
$ sparrowdo --sparrowfile=utils/install-sparky-web-systemd.pl6 --local_mode 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，我们将看到 Sparky UI 在 [http://localhost:3000](http://localhost:3000) 可用，sparkyd 守护进程在进程列表中显示为一个进程:

```
$ ps uax | grep sparkyd
root       9511  3.3 10.0 574440 202988 ?       Ssl  14:38   7:35 /opt/rakudo-pkg/bin/moar --execname=/opt/rakudo-pkg/bin//perl6 --libpath=/opt/rakudo-pkg/share/nqp/lib --libpath=/opt/rakudo-pkg/share/nqp/lib --libpath=/opt/rakudo-pkg/share/perl6/lib --libpath=/opt/rakudo-pkg/share/perl6/runtime /opt/rakudo-pkg/share/perl6/runtime/perl6.moarvm /opt/rakudo-pkg/share/perl6/site/bin/sparkyd --root=/root/projects/devops/sparky 
```

Enter fullscreen mode Exit fullscreen mode

现在是最激动人心的部分——设置第一个任务，运行它并查看结果！

# 火花任务

正如 sain 所说，Sparky task 只是 Sparrowdo 的一个场景，如果你不熟悉[sarrow do](https://github.com/melezhik/sparrowdo)-不要担心有很多关于这个工具的帖子。但对我们来说只是一个方便的 [DSL](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md) 来创建用户场景:

```
 $ nano project1/sparrowfile

#!perl6

bash "echo Hello! My name is Sparky!"; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的任务，准备用 Sparky 运行，但首先我们需要设置 crontab 入口，这很简单，这只是一个很好的旧 crontab 风格:

```
$ nano project1/sparky.yaml

# what about running every minute? 
crontab: "* * * * *" 
```

Enter fullscreen mode Exit fullscreen mode

我没有这么说，但是我们应该在*的某个*目录下保存两个文件(`sparrowfile`和`sparky.yaml`)，该目录位于`$SPARKY_ROOT`目录下，默认为`~/.sparky/projects`:

```
 $ ls -d ~/.sparky/projects
project1
project2
project3 
```

Enter fullscreen mode Exit fullscreen mode

因此 sparkyd 读取位于`$SPARKE_ROOT`的所有子目录(1 级深度)并执行找到的 sparky 任务。`$SPARKE_ROOT`可以通过环境变量配置，或者作为 sparky 任务运行器守护进程的命令行选项:

```
$ sparkyd --root=/path/to/sparky/root/directory 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们去 WEB UI 看看我们第一个任务的结果:

```
running sparrow tasks on 127.0.0.1 ... 

target OS is - centos7
push [task] run bash: echo 'Hello! My name is Sparky!' ... OK
SPL file /opt/sparky-sparrowdo/project1/sparrow.list is empty
set up task box file - /home/melezhik/.sparrowdo//opt/sparky-sparrowdo/project1/task-box.json - OK

found ini file: /home/melezhik/sparrowdo.ini
sparrow root: [/opt/sparky-sparrowdo/project1]
/opt/sparky-sparrowdo/project1/sparrow.index updated OK from https://sparrowhub.org
sparrow root: [/opt/sparky-sparrowdo/project1]
public@bash is uptodate (0.1.8)
running task box from /opt/sparky-sparrowdo/project1/sparrow-cache/task-box.json ... 
@ runs bash command
2018-10-19 19:25:01 : [task] run bash: echo Hello! [path] /modules/bash-command/ [params] envvars:
Hello! My name is Sparky!
ok  scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以看到任务成功完成，到目前为止，我们在 localhost 上运行任务，但我们不受限制。Sparkly 还能够远程运行任务:

## 通过 ssh

```
$ nano sparky.yaml

sparrowdo:
  host: '192.168.0.1'
  ssh_private_key: /path/to/ssh_private/key.pem
  ssh_user: sparky 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Docker 上

```
$ nano sparky.yaml

sparrowdo:
  docker: 'black_horse' 
```

Enter fullscreen mode Exit fullscreen mode

# 我还没说什么:

只是这篇文章中没有提到的亮点

*   [Sparky 插件](https://github.com/melezhik/sparky#sparky-plugins)扩展基本功能(任务完成后发送通知邮件等等)

*   [配置](https://github.com/melezhik/sparky#configure-sparky-workers)火花任务

*   管理旧报告

*   禁用任务

*   使用 [mysql/postgresql](https://github.com/melezhik/sparky#running-under-other-databases-engines-mysql-postgresql) 数据库引擎

*   使用[任务运行器](https://github.com/melezhik/sparky#command-line-client)调试火花任务。

*   [Docker image](https://github.com/melezhik/sparky-docker)Sparky 准备就绪

一如既往，非常感谢您的评论和反馈。****