# 用打开的风扇制作球拍

> 原文：<https://dev.to/dvdmuckle/making-a-racket-with-openfaas--4mmf>

[![Image result for racket lang](img/d0ece509cb191e837e77e803396dd274.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---3RhipEC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://racket-lang.org/img/racket-logo.svg)

云计算正在发展，随之而来的是一大堆新的框架和术语，比如“无服务器”和“功能即服务”。虽然很多这样的框架是由亚马逊和谷歌这样的大公司提供的，但也有一些项目，如 OpenFaaS，允许你在自己的机器上运行一个功能作为服务平台。此外，OpenFaaS 还允许轻松扩展平台以支持其他语言。因为 OpenFaaS 围绕着函数，所以通过使用美妙的球拍环境来添加对第一种函数式编程语言 Lisp 的支持是非常有意义的。现在你也可以制作自己的无服务器球拍功能了！让我们试一试。

### 设置环境

如果你已经设置了 OpenFaaS，很好，你可以跳过这一节！如果你的机器上已经有 Docker，你可以跳过下一段。我们将设置 OpenFaaS 在 Docker 上运行。如果你想在其他东西上运行它，请查看官方文档。OpenFaas 支持 Kubernetes 和 Docker Swarm。如果你不想在本地机器上安装 Docker，而只是想在一个短暂的在线环境中暂时试用 OpenFaaS，你也可以使用 [Play With Docker](http://play-with-docker.com) 。虽然你不需要它，但如果你想让世界其他地方的人也能使用你的功能，或者你打算在一台不同于你要部署的机器上构建你的功能，拥有一个 [Docker Hub](http://hub.docker.com) 账户可能会有所帮助。

#### 安装对接器

Docker 是一个允许您构建和启动容器的工具:包含给定应用程序的所有库和代码的文件系统映像。Docker 容器可以在任何安装了 Docker 的机器上运行，这使得它对于运输和部署应用程序来说非常强大。我们将使用它来运行和构建 OpenFaaS 函数。如果你有 Linux 系统，可以做`curl https://get.docker.com | bash`或者`curl -o docker.sh https://get.docker.com && bash docker.sh`安装 Docker。请注意该命令的输出，尤其是关于`docker`组的内容。如果你有苹果电脑，点击[这里](https://docs.docker.com/docker-for-mac/install/)来安装 Docker，它的安装和其他应用程序一样。如果你有 Windows，没有 Windows 10 的 Pro 版本，可以使用 [Docker 工具箱](https://docs.docker.com/toolbox/toolbox_install_windows/)。

*边注:如果你想要一个更永久和可管理的测试 OpenFaaS 的方法，我强烈建议使用 [Docker Machine](https://docs.docker.com/machine/) 来创建一个只针对 OpenFaaS 的 VM。*

#### 部署 OpenFaaS

由于 OpenFaaS 被设计成在多台机器的集群中运行，我们将把我们的机器设计成 Docker 群管理器。如果您只有一台机器用于测试，这没问题，但是如果您想在生产中部署 OpenFaaS，强烈建议您使用一些具有多台机器的容错集群。要使您的机器成为群管理器，您可以运行`docker swarm init`或`docker swarm init --advertise-addr eth0`，这取决于前一个命令的输出。现在我们可以用`git clone https://github.com/openfaas/faas && cd faas && git checkout 0.7.5 && ./deploy_stack.sh`部署 OpenFaaS。你可以看到 OpenFaaS 的部件随着`docker service ls`旋转起来。你现在可以用你的网络浏览器访问`http://<docker host ip>:8080`。如果您在 Linux 或 Mac 上运行 Docker，`localhost`是您的 Docker 主机。在 Windows 上，用 Docker 工具箱，会是一些`192.168.0.0/16` IP。可以用`docker system info | grep 'Node Address'`查看 IP。如果您使用的是 Play With Docker，您应该会在页面顶部附近看到一个指向 8080 端口的链接。

从 OpenFaaS 仪表板，您可以从函数存储中部署新函数，以及调用函数。如果函数需要一些输入，您也可以使用`curl http://<docker host ip>:8080/function/<function name>`或`curl -XPOST -d <data> http://<docker host ip>:8080/function/<function name>`通过`curl`调用这些函数。

既然平台已经设置好了，我们需要让客户端工具与 OpenFaaS 交互。你可以用`curl -sL cli.openfaas.com | sh`这样做。这将为您提供`faas-cli`工具，用于构建、部署和调用函数。

### 创建我们的球拍功能

现在我们的环境已经设置好了，我们可以开始创建函数了。为您的函数创建一个新文件夹，并在其中运行`faas-cli template pull https://github.com/dvdmuckle/racket-template`。这将引入球拍功能的框架代码，以及一些支持文件。接下来，运行`faas-cli new <function name> --lang racket`，其中`function name`是您正在创建的函数的名称。不要担心它与您之前创建的文件夹相匹配。

这将创建一些工件:一个以您的函数命名的文件夹，和一个`<function name>.yml`文件。YAML 文件指定了函数的名称、它是什么语言、将要构建的 Docker 映像的名称、可以访问 OpenFaaS 设置的 URL 以及其他一些东西。根据您的设置，如果您的 OpenFaaS 设置不在您将要构建函数的同一台机器上，您可能希望更改图像的值。

在创建的文件夹中，您会发现一个`handler.rkt`文件。让我们看看它有什么。

```
#lang racket
(provide handle)
(define (handle) (define in (read-line))
 (display "You said ")
 (displayln in)) 
```

Enter fullscreen mode Exit fullscreen mode

虽然所有这些都是回应它接收到的任何输入，但它给了我们一个很好的框架来研究。这里必须始终存在两件事:一个是语言声明，其中使用了`racket`，另一个是`(provide handle)`，它只是说，“这个文件提供了这个函数。”这是必要的，因为这个处理程序文件被另一个文件用来获取`handle`函数定义，它必须总是存在并且是一个顶级函数。除此之外，这个文件可以包含您想要的任何其他内容。如果你愿意，你可以在与`handler.rkt`相同的文件夹中添加更多的文件，使用`require`和`provide`来确保它们都知道彼此。请注意，输入是通过`read-line`从`stdin`收集的。如果`handle`有任何参数，函数将无法构建。

虽然按原样构建和部署这个功能是非常好的，但是有点无聊。计算斐波纳契数怎么样？

```
#lang scheme
(provide handle)
(define (handle) (define in (read-line))
  (define (fib n)
    (define (fof n a b)
      (cond ((= n 0) a)
            ((= n 1) b)
            (#t (fof (- n 1) b (+ a b)))))
   (fof n 0 1))
 (define num (string->number (if (eof-object? in) (error "Requires input") in)))
 (if (number? num) (displayln (fib num)) (error "Input not a number"))) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个尾部递归斐波那契数列，我们把它作为错误校验输入。然后将结果打印到`stdout`。注意，一旦构建和部署了函数，`stdin`和`stdout`基本上分别成为 HTTP 数据发布和响应主体。这也是 OpenFaaS 相当漂亮的部分原因。您可以创建基于 web 的功能，而不必担心 web 部件，只需要担心功能部件。

如果您有一个本地的球拍环境，您可以通过将它加载到球拍会话中来测试这个函数，并以这种方式调用这个函数。但是，因为我喜欢凭感觉飞行，所以我们现在就要构建并部署它。

### 构建和部署功能

一旦你的功能都设置好了，我们就可以构建它了。使用`faas-cli build -f <function name>.yml`构建函数。确保在构建之前对 YAML 文件进行了任何必要的编辑，如更改图像值。如果您构建并部署到同一台机器上，这并不重要。现在您的函数已经构建好了，您可以用`faas-cli deploy -f <function name>.yml --gateway http://<docker host ip>:8080`来部署它。如果您已经编辑了 YAML 文件中的网关值，那么您可以省去`--gateway`标志。如果缺省值`http://localhost:8080`确实是 OpenFaaS 所在的位置，您也可以省略它。

现在，是时候看看我们的劳动成果了！先试试`curl -XPOST -d 3 http://<docker host ip>:8080/function/<function name>`。如果你的函数确实计算了一个给定数字的斐波那契数，你应该返回`2`。您也可以在之前打开的 OpenFaaS UI 中尝试一下。一个新的功能将会出现，你的功能！您可以在那里输入相同的输入，调用它，并得到相同的响应。你也可以使用`faas-cli invoke <function name> --gateway http://<docker host ip>:8080`来调用你的函数

### 接下来是什么？

既然你已经看到了如何创建无服务器球拍功能，我建议你创建更多！Lisp 和 rack 在我看来都很优秀，我个人希望继续给这个模板添加更多的特性，比如安装外部的 rack 库。作为一种语言，Lisp 一开始看起来语法上很复杂，但是坚持足够长的时间，你很快就会看到它的优点。或者也许你会在睡梦中看到括号…

用 OpenFaaS 制作球拍的帖子最早出现在 [dvdmuckle](https://dvdmuckle.xyz) 上。