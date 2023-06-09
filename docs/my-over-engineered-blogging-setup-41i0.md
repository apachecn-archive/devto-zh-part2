# 我过度设计的博客设置

> 原文：<https://dev.to/_gdelgado/my-over-engineered-blogging-setup-41i0>

最初发布于 [gdelgado.ca](https://gdelgado.ca/my-over-engineered-blogging-setup.html#title)

在这篇文章中，我描述了我是如何更新我的 [Pelican](https://blog.getpelican.com/) 博客设置的。

不久前，我的网站的 HTTP 请求由于某种原因停止工作。这是我的 CDN (cloudflare)的一个错误。对我来说，这是来自上帝的信号，是时候做一些不必要的复杂的事情来解决问题了。

不过说真的。有些事情我想解决很久了，但一直没有时间去做。当前的 CDN 问题+一个长周末似乎是这样做的正确时间。

**tldr** :

*   用(sass + python 和所有的 pelican 依赖项)创建了一个定制的 docker 映像
*   使用前面提到的 docker 图像将整个项目[归档](https://github.com/gDelgado14/personal-site/blob/master/Dockerfile#L1)
*   设置自动部署(使用 [CircleCI](https://circleci.com/) )将博客部署到[网上](https://www.netlify.com/)

### 依赖地狱

关于鹈鹕博客为当地发展而建立的方式，有一些事情困扰着我。鹈鹕医生建议你安装 T2 虚拟人。这工作得很好，直到我决定我想要一个更好的 CSS 开发体验。我把 CSS 切换到 SASS，但是这意味着我必须安装 ruby，因为它是 ruby 的瑰宝。

好了，现在我已经在全球范围内安装了 python、virtualenv 和 Ruby，这样我就可以在本地运行博客了。这也使自动化部署变得复杂，因为您必须安装这些依赖项来确保构建博客成功。

### 码头工人前来救援

我真的不喜欢全球安装依赖关系。所以我觉得这是个和 Docker 一起玩的好机会。

我会饶你所有的中间版本，我的 docker 设置，并得到你目前在回购中看到的。

有三个与 docker 相关的文件:

*   `Dockerfile-push`
*   `Dockerfile`
*   `docker-compose.yml`

包含创建可重用映像的说明(我在 CI 流程中重用了该映像。稍后将详细介绍)。这张图片住在 https://hub.docker.com/r/giorgio14/pelican/...图像相当大，我知道。那又怎样？告我吧。

现在我有了一个运行 pelican 及其所有依赖项的基础映像，我可以从它创建一个运行开发服务器的新映像。这就是`Dockerfile`的用武之地。在里面我:

*   创建图像`FROM`我的定制乔治 14/鹈鹕图像
*   `COPY`将相关文件和目录放入镜像中
*   将我的操作系统的端口 8080 绑定到容器的端口 8080
*   运行一个`make`脚本，该脚本在端口 8080 上运行一个实时重载服务器

只有一个问题。如果我要从这个图像创建一个容器，这个容器会创建我的文件的副本，但是它不会链接回它们。换句话说；容器有自己的副本，与我的操作系统上的副本完全无关。这意味着尽管有一个监听文件变化的实时重载服务器，但容器中实际上没有文件会发生变化。

我们需要[卷](https://docs.docker.com/storage/volumes/)。这是一种允许容器访问主机操作系统的文件和目录的机制。我使用卷，以便可以编辑`theme`和`content`目录中的任何内容，并将这些更改传播到正在运行的 docker 容器。一旦容器中发生了变化，容器中运行的 python 服务器就可以重新生成博客并提供更新的文件。

为此，我使用了`docker-compose.yml`文件。其中有一个`volumes`键，它描述了我希望将哪些本地目录挂载到正在运行的容器中。

现在这些都完成了，我可以简单地运行:

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

码头工人将:

*   提取并生成所需的图像
*   在容器内运行`make devserver PORT=8080`(来自`Dockerfile`)

现在我有一个 schnazzy 开发设置，不需要 python，virtualenv，ruby 或任何东西:)...只有码头工人！

### 自动部署

现在，我在 hub.docker.com 上托管了一个可重用的 docker 映像，我可以利用它来使我的 CI 步骤变得超级简单。

我的目标是让我的网站更新我回购的主分支机构的每一个新的推送。我选择 CircleCI 是因为它的品牌、优秀的文档(我不到一天就学会了如何使用它)和免费的价格🙃。

我基本上只是按照文档将我的 github repo 连接到 circle，并让“ [hello world](https://circleci.com/docs/2.0/hello-world/) ”管道在此后不久运行。

[这里的](https://github.com/gDelgado14/personal-site/tree/master/.circleci)是与 circle 关联的相关文件。我花了一些尝试和调整来让管道按照我想要的方式运行，所以我创建了一个 bash 脚本，它允许我在不将提交推送到 github 的情况下运行管道。[这是](https://circleci.com/docs/2.0/examples/)圈子里的相关文件。

我将提到一些关于`config.yml`文件的高层次观点。请注意，我使用了上面制作的自定义图像。不需要安装 ruby，不需要做 apt-get，或者任何东西。安装好`pelican` cli 后，一切都准备就绪。

因为我想部署到 netlify，所以我必须安装他们的 cli。我选择了他们当前的稳定 cli (repo [here](https://github.com/netlify/netlifyctl) )而不是他们基于节点的 cli，因为我对它已经很熟悉了。

然后我使用`make publish`生成博客的生产版本。

为了将来的调试，我想我会保存`make publish`命令的输出，以防发生奇怪的事情。Circle 的用户界面允许我在需要的时候访问压缩目录。

最后我部署到 netlify。

我通过创建一个指向随机生成的 netlify URL 的 CNAME 记录，在 netlify 上设置了一个自定义域。这里有更多关于 [netlify](https://www.netlify.com/docs/custom-domains/) 自定义域名的信息。

### 仍待琢磨

我很想在我的网站上有评论，但我肯定不想用 Disqus。当我有机会的时候，我会给静态人一个机会。

[静态人+鹈鹕教程](https://snipcart.com/blog/pelican-blog-tutorial-search-comments)

...就是这样！