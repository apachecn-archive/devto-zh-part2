# 空壳里的幽灵第四部分(也是最后一部分):维护

> 原文：<https://dev.to/ebreton/ghost-in-a-shell---part-iv-and-final-maintenance-e6o>

[![Ghost in A shell - part IV (and final): Maintenance](img/10fc8be98c20924c3c3d3daefef69a34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8a5pRpXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/conteimg/2018/06/GIAS-maintenance.png)

选择 Ghost(Pro) 的一个很好的理由是，你不必维护你的博客。他们只是跑。永远。即使服务器崩溃，硬盘损坏，电力中断，博客也需要升级...他们将继续服务他们的页面。

然而，拥有自己的设置是有趣的(只要你知道上面的限制)，而且更容易玩。例如，您可以:

*   运行您想要的 Ghost 版本:

```
$ GHOST_VERSION=1.10 PORT=3002 NAME=old make

$ NAME=old make cli-version
  docker exec -it old ghost -v
  Ghost-CLI version: 1.1.1
  Ghost Version (at /var/lib/ghost): 1.10.0
  Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

*   维护许多博客，而不必记住它们创建时的选项:

```
# instead of 
$ PORT=3003 NAME=new URI=my-new-super-blog DOMAIN=me.com make restart

# just use
$ p=instances/new/ make restart 
```

Enter fullscreen mode Exit fullscreen mode

*   升级您的博客没有风险，没有麻烦

```
$ vi instances/old/.env
  GHOST_VERSION=1.24

$ make p=instances/old/ upgrade
  docker exec -it old ghost -v
  Ghost-CLI version: 1.8.1
  Ghost Version (at /var/lib/ghost): 1.24.2
  Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

这三个理由足以让我写第四篇(也是最后一篇)关于《幽灵的贝壳》的博客。我说“第四”了吗？没错。。这是前三个:

1.  第一部分:本地主机(localhost)，重点介绍本地主机
2.  第二部分:HTTPs 是规范，它向您展示了如何在给定的服务器上生成多个 ghost 实例
3.  [第三部分:生产，性能](https://dev.to/ebreton/ghost-in-a-shell---part-iii--to-production-with-performance-10g5)，应用 Ghost 团队关于 d b+ email 的建议，并对结果实例施加一点压力

但是首先你需要基本的诊断工具

# 环境变量

我们之前已经看到了以下两个命令

*   `make ps`列出正在运行的实例
*   跟踪日志，看看发生了什么

这些命令取决于环境变量，可以在命令行中覆盖这些变量。例如:

1.  `make vars`将为您列出当前配置的变量

```
$ make vars
# common
 NAME=ghost-local
 GHOST_VERSION=1
# values used for dev
 PORT=3001
# values used by traefik (qa & prod)
 PROTOCOL=http
 DOMAIN=localhost
 URI=ghost-local 
```

Enter fullscreen mode Exit fullscreen mode

1.  `NAME=my-blog URI=me PROTOCOL=https GHOST_VERSION=1.22 make vars`将显示变量是如何被覆盖的

```
$ make vars
# common
 NAME=my-blog
 GHOST_VERSION=1.22
# values used for dev
 PORT=3001
# values used by traefik (qa & prod)
 PROTOCOL=https
 DOMAIN=localhost
 URI=me 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止(在以前的博客中),这是处理多个实例的方法...这很糟糕，尤其是如果你想为它们中的每一个运行不同的值集(这正是我们首先有这个系列的原因)

# 一体机配置

因此，在可用变量列表中出现了一个新人:有请`p`。

> `p`是您想要操作的博客的有效实例的路径。到目前为止，您可以在我们看到的大多数`make`命令中使用它。

*有效的*意味着路径将包含一个*。包含所有被覆盖变量的 env* 文件。为了您的理智起见，当您用`make [dev|qa|prod]`生成实例时，这个文件是自动生成的。例如:

```
$ NAME=new-blog PORT=3002 make
# Simply start a ghost container making it directly available through $PORT
docker run --rm -d --name new-blog \
        -v /Users/emb/git-repos/ghost-in-a-shell/instances/new-blog:/var/lib/ghost/content \
        -p 3002:2368 \
        -e url=http://localhost:3002 \
        ghost:1-alpine
6330f60a50a834c7acee5916751815e47bba2f68b98f150a6ba2fe59627704ff
make save-vars
creating instances/new-blog/.env 
```

Enter fullscreen mode Exit fullscreen mode

最后两行是新的甜心...现在，您可以运行以下命令来确保您的配置已经保存:

```
$ make p=instances/new-blog/ vars
# common
  p=instances/new-blog/.env
  NAME=new-blog
  GHOST_VERSION=1
# values used for dev
  PORT=3002
# values used by traefik (qa & prod)
  PROTOCOL=http
  DOMAIN=localhost
  URI=new-blog 
```

Enter fullscreen mode Exit fullscreen mode

因此

*   `make p=instances/new-blog/ vars`将列出来自*的所有变量。/instances/new-blog/。环境*
*   `make p=instances/new-blog/ logs`将跟踪并跟随*的日志。/instances/new-blog* 博客实例
*   `make p=instances/new-blog/ restart`将使用*中定义的设置重新启动博客。/instances/new-blog/。环境*
*   `make p=instances/new-blog/ cli-version`显示新博客的版本
*   等等...

> 注意，1)路径是相对的，2)结尾的`/`是强制的，但是无论如何它应该与 bash 自动完成一起出现。(所以才这样做)

# 升级(和降级)

升级可能会很痛苦(尤其是如果你做得不够频繁的话)。这确实很可怕。没有人想改变(打破)运行顺畅的东西。

你首先需要一些信息，比如问“我落后了多远？”。`make cli-version`会在那里帮你:

```
$ make cli-version
docker exec -it ghost-local ghost -v
Ghost-CLI version: 1.8.0
Ghost Version (at /var/lib/ghost): 1.23.0
Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

升级机制依赖于 [DockerHub 标签](https://hub.docker.com/r/library/ghost/tags/)。这意味着最通用的标签指向最新版本，并且同一版本可能有多个标签。例如，如果最新版本是 1.24.2，那么您将获得带有以下所有标签的版本:1.24.2，1.24，1，latest。

因此，升级时会出现以下行为:

*   `GHOST_VERSION=1` - >将自动更新到第一版的所有新版本
*   `GHOST_VERSION=1.x` - >将仅针对补丁自动更新
*   `GHOST_VERSION=1.x.x` - >永远不会自动更新

默认情况下，GHOST_VERSION 设置为`1`。所以升级就像`make upgrade[-qa|-prod]`一样简单。该命令将提取最新的(1.x.x) docker 映像，并为您重新启动容器。

您可以通过覆盖 env var:
来强制您想要的 GHOST_VERSION

```
$ GHOST_VERSION=1.24 make upgrade
...
Ghost-CLI version: 1.8.0
Ghost Version (at /var/lib/ghost): 1.24.2
Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

如果在创建博客实例时指定了一个 GHOST_VERSION，例如:`GHOST_VERSION=1.10 PORT=3002 NAME=old make`，那么这个版本就存储在*中。env* 文件。因此`p`助手变量不允许你自动升级。它将坚持创建时指定的实例版本，例如:

```
$ p=instances/old/ make upgrade
...
docker exec -it old ghost -v
Ghost-CLI version: 1.1.1
Ghost Version (at /var/lib/ghost): 1.10.0
Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

您必须手动编辑您的*。/instance/$NAME/。env* 文件并设置新的 GHOST_VERSION 来升级它。

您是否希望回滚到以前的版本，例如版本 1.23.0？只需针对*进行修改。env* 文件并再次升级。或者通过命令行传递变量。

```
$ vi instances/old/.env
GHOST_VERSION=1.23.0

$ make p=instances/old/ upgrade
docker exec -it old ghost -v
Ghost-CLI version: 1.8.1
Ghost Version (at /var/lib/ghost): 1.23.0
Latest version on Docker Hub: 1.24.2 
```

Enter fullscreen mode Exit fullscreen mode

> 实际下载的 docker 图片是`ghost:1.x.x-alpine`:这里加上了 *-alpine* 后缀，因为我喜欢它更轻而不是更重。

# 可靠性？

嗯，我们已经看到了相当多的这些系列和可靠性有更多的与主机您将选择。以及您将要实施的安全策略。对于这样一个简单的教程来说太多太广了。还记得一开始的免责声明:如果你在寻找可靠性，请选择 Ghost pro:)

干杯，
马努