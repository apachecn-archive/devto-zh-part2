# 使用 docker 云将 Flask 和 Logstash 应用程序部署到数字海洋。

> 原文：<https://dev.to/jj/deploying-a-flask-and-logstash-application-to-digital-ocean-using-docker-cloud--1pe7>

[![Deployed in the cloud](../Images/a8905dc134e25b334402195afe83255b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cfMeA5cr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/98dqd9ul4lgufewovd1d.jpg)

您知道每个应用程序都必须做的一件事吗？日志。即使你没有大量的数据，你也必须知道每个人都在用你的东西做什么。而 [Logstash](https://www.elastic.co/products/logstash) 可能是最好的选择之一。除了存储你的数据之外，它还是 [ELK stack](https://www.elastic.co/elk-stack) 的一部分，后者包括弹性数据存储和 Kibana dashboard 构建工具包。
所以，当创建一个 web 应用程序时，你也可以使用这个(或者其他的)日志服务。这就是我们要对这个应用程序做的事情，这个应用程序最初是作为一个 [Platzi](https://platzi.com) 课程的示例而创建的。

## 然先，云。

[Docker cloud](https://docs.docker.com/docker-cloud/) 是一个极其好的想法。这是一个统一的 web、API 和 CLI 桥梁，可以与任何云供应商一起使用容器。您不再需要担心特定的模板或特定的命令行界面。一个 CLI 来管理它们:T2 Docker cloud CLI T3，由于某种原因，它只能在 Python 2.x 上运行

作为一座桥梁，你必须提供另一边的东西:你[带来自己的云](https://docs.docker.com/docker-cloud/getting-started/connect-infra/)。我确实可以使用其中的一些，但是[数字海洋](https://docs.docker.com/docker-cloud/infrastructure/link-do/)给了我一张优惠券，设置起来很容易。

> 如果你想自己试一试，[这个链接和我的推荐代码](https://m.do.co/c/a5b332c5e855)会让你得到 10 美元，我得到 25 美元，在你再次签约后，继续试验和创造这个和其他的教程。感谢使用！

好了，宣传够了。让我们假设您已经设置好了一切，并且已经下载了 docker-cloud CLI 并将 Python 切换到 2.7 版本以使其工作。然后，您需要使用登录您的帐户

```
docker login 
```

Enter fullscreen mode Exit fullscreen mode

您将需要创建一个相当大的节点来保存 logstash。这是在 Java 中完成的，而 Java 通常占用大量内存。这就行了

```
docker-cloud nodecluster create -t 1 --tag platzi platzi-big digitalocean fra1 2gb 
```

Enter fullscreen mode Exit fullscreen mode

这是在法兰克福的第一个数据中心(`fra1`)中使用提供者`digitalocean`并使用`2gb`实例大小创建一个名为 platzi-big 的节点。最后这两个是最棘手的，知道所有事物的确切名称。然而，这个[程序通过 Docker cloud API](https://github.com/JJ/platzi-servicio-web/blob/master/utils/get-regions.py) 返回所有可用的数据中心和实例，因此您可以轻松地进行挑选。这实际上是不同供应商之间的差异之一:数据中心和实例都将有不同的名称。除此之外，docker-cloud 提供了一个很好的无缝接口来将你的应用程序部署到云上。

## 准备出发

> **剧透**:你可以在[这个 GitHub 回购](https://github.com/JJ/platzi-servicio-web)里得到整件事。

在 Docker 中，每样东西都有自己的容器。烧瓶去一个，Logstash 去另一个。您需要将 Flask 连接到 Logstash，log stash 提供了多种方法来实现这一点。我们将使用一个端口向它发送 JSON 日志，但是为了这样做，我们需要配置 Green Unicorn，这个应用程序实际上正在启动 web 服务并处理日志。这个 [`gunicorn-logging.conf`](https://github.com/JJ/platzi-servicio-web/blob/master/gunicorn_logging.conf) 就行了。

```
[loggers]
keys=root, gunicorn.error, gunicorn.access

[handlers]
keys=console, logstash

[formatters]
keys=json

[logger_root]
level=INFO
handlers=console

[logger_gunicorn.error]
level=ERROR
handlers=console
propagate=0
qualname=gunicorn.error

[logger_gunicorn.access]
level=INFO
handlers=logstash
propagate=0
qualname=gunicorn.access

[handler_console]
class=StreamHandler
formatter=json
args=(sys.stdout, )

[handler_logstash]
class=logstash.TCPLogstashHandler
formatter=json
args=('logstash',5959)

[formatter_json]
class=jsonlogging.JSONFormatter 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件重要的事情需要注意。您必须定义一个 Logstash 处理程序，对其进行配置，并配置该处理程序中使用的格式化程序，该程序将使用 JSON。表示您将使用一个监听 TCP 端口的驱动程序。用`args`你告诉它机器的名字，我们称之为`logstash`，以及它将要监听的端口。当然，所有这些都必须在项目的[需求](https://github.com/JJ/platzi-servicio-web/blob/master/requirements.txt)中考虑，这里我们包括用于日志记录的`json-logging-py`和`python3-logstash`，当我们这样配置`gunicorn`时，我们会隐式地导入它们。因为这是在那个层次上完成的，我们不需要在我们的应用程序中做任何改变。它会自己变魔术。

## 登上云端

Docker cloud 使用一个叫做[堆栈文件](https://docs.docker.com/docker-cloud/apps/stack-yaml-reference/)的东西来配置部署。除了一些事情之外，它基本上类似于 Docker 编写文件；主要的一点是 Docker-cloud 不从 Docker 文件构建映像。它们一定藏在某个地方。最简单的方法是将它们存储在公共注册中心，但是当然你也可以使用私有注册中心，特别是那些由云供应商自己提供的注册中心。我们定义了这个非常简单的堆栈文件:

```
logstash:
  image: docker.elastic.co/logstash/logstash-oss:6.2.1
  expose:
    - "5959"
  command: -e 'input { tcp {  port => 5959  codec => json   } } output { stdout {} }'

web:
  image: jjmerelo/platzi-servicio-web
  ports:
    - "80:80"
  links:
    - logstash 
```

Enter fullscreen mode Exit fullscreen mode

有两个容器，一个将由 Elastic 提供的官方映像部署。在这个我们称之为`logstash`的容器中，我们公开我们希望日志到达的端口，并用`-e`定义输入和输出配置文件。我知道，这将把日志打印成标准输出。你可能会想把它连接到其他地方来储存它们，用它做一些很酷的事情。有几个[例子](https://github.com/deviantony/docker-elk)说明如何做到这一点，所以我们将把它留给下一个教程。`logstash`也将是这台机器的名字。记得我们在`gunicorn`日志配置中使用了这个名称。

Flask 部分的配置包括将从 Docker Hub 获取的图像、端口的定义，以便我们可以向外部发布它，以及重要的部分:`links`到`logstash`，它在两者之间创建了一个[桥接网络](https://docs.docker.com/network/bridge/)。

仅此而已。运行`docker-cloud stack up`和`docker-cloud stack inspect long-number-returned-by-the-prev-command+`会告诉你发生了什么。他们可能需要一段时间来启动，主要是 logstash。那个`inspect`命令会告诉你服务的 UUID，`docker-cloud service logs long-UUID`会在 logstash 容器中返回类似这样的内容:

```
logstash-1 | 2018-02-23T17:34:50.133858365Z [2018-02-23T17:34:50,133][INFO ][logstash.inputs.tcp      ] Starting tcp input listener {:address=>"0.0.0.0:5959", :ssl_enable=>"false"}
logstash-1 | 2018-02-23T17:34:50.464871650Z [2018-02-23T17:34:50,464][INFO ][logstash.pipeline        ] Pipeline started succesfully {:pipeline_id=>"main", :thread=>"#<Thread:0x4a26bdaa run>"}
logstash-1 | 2018-02-23T17:34:50.653066642Z [2018-02-23T17:34:50,650][INFO ][logstash.agent           ] Pipelines running {:count=>1, :pipelines=>["main"]} 
```

Enter fullscreen mode Exit fullscreen mode

## 饿了求更？

例如，查看[如何使用 docker-cloud](https://lostechies.com/gabrielschenker/2016/04/07/blue-green-deployment-in-docker-cloud/) 扩展、重新部署和创建蓝绿色部署。或者等到本教程的下一个实例。

> 此外，如果你刚刚为节目做了，记得`terminate`你的栈和节点。这样就可以摆脱容器了。然后终止您已经启动的节点。以上所有的东西可能会花掉你几分钱，但是要记得在你真正需要的时候留着。