# Hugo 和 Nginx 多阶段构建 Dockerfile

> 原文：<https://dev.to/eduardort/hugo-and-nginx-multi-stage-build-dockerfile-3o63>

*最初发布于[雷耶斯](https://reyes.im/post/docker-hugo-image/)。*

* * *

搜索了一会儿后，我无法找到一个很好的预制 docker 文件来服务我的个人网站(构建在 [Hugo](https://gohugo.io/) 之上)，我找到的一些图像只是 Hugo 构建步骤，其他一些能够服务和构建网站，但他们删除了`FROM:ubuntu` docker 反模式。

所以在这里我将描述我在最后的 **Dockerfile** 上做的事情，这是一个非常简单的[Docker](https://www.docker.com/)T4】多阶段构建，第一步获取 [Hugo](https://gohugo.io/) 二进制文件并构建站点，第二步复制已构建站点的公共文件夹并使用官方的 alpine [Nginx 映像](https://hub.docker.com/_/nginx/)提供服务。

# 什么是多阶段构建？

> 对于多阶段构建，可以在 docker 文件中使用多个 FROM 语句。每个 FROM 指令可以使用不同的基础，并且每个指令都开始构建的新阶段。您可以有选择地将工件从一个阶段复制到另一个阶段，在最终图像中留下所有不想要的东西。为了展示这是如何工作的，让我们修改前一节中的 docker 文件以使用多阶段构建。
> 
> [Docker](https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds)

# 雨果建步

```
FROM alpine:3.5 as build

ENV HUGO_VERSION 0.41
ENV HUGO_BINARY hugo_${HUGO_VERSION}_Linux-64bit.tar.gz

# Install Hugo
RUN set -x && \
  apk add --update wget ca-certificates && \
  wget https://github.com/spf13/hugo/releases/download/v${HUGO_VERSION}/${HUGO_BINARY} && \
  tar xzf ${HUGO_BINARY} && \
  rm -r ${HUGO_BINARY} && \
  mv hugo /usr/bin && \
  apk del wget ca-certificates && \
  rm /var/cache/apk/*

COPY ./ /site

WORKDIR /site

RUN /usr/bin/hugo 
```

Enter fullscreen mode Exit fullscreen mode

第一部分`FROM alpine:3.5 as build`将构建步骤标记为`build`，以便在下一步中我们可以从之前的图像中获取工件，我们将保留的工件是构建的站点。

```
ENV HUGO_VERSION 0.41
ENV HUGO_BINARY hugo_${HUGO_VERSION}_Linux-64bit.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

这是相当不言自明的，声明 Hugo 版本并使用它来获得我们将从官方 GitHub 版本中获得的二进制名称

```
RUN set -x && \
  apk add --update wget ca-certificates && \
  wget https://github.com/spf13/hugo/releases/download/v${HUGO_VERSION}/${HUGO_BINARY} && \
  tar xzf ${HUGO_BINARY} && \
  rm -r ${HUGO_BINARY} && \
  mv hugo /usr/bin && \
  apk del wget ca-certificates && \
  rm /var/cache/apk/* 
```

Enter fullscreen mode Exit fullscreen mode

`set -x`将为我们提供这些命令的良好输出，然后我们更新 repos 并从 alpine 仓库安装`wget`和`ca-certificates`，之后我们从 GitHub releases 页面构建发布二进制文件下载 URL 并使用 wget 下载它，然后我们解压缩 tar 文件，删除它并将二进制文件移动到/usr/bin 文件夹，之后我们做一些标准的清理任务，毫不费力地将最终图像压缩到较小的大小。

```
COPY ./ /site

WORKDIR /site

RUN /usr/bin/hugo 
```

Enter fullscreen mode Exit fullscreen mode

现在我们正在复制当前目录(应该是 Hugo 站点的根文件夹)并将其移动到/site(在容器内)，接下来我们基本上做了一个将 dir 更改为`/site`的操作，最后一步是针对/site 实际运行 Hugo 二进制文件，并获得最终的`/public`文件夹，其中包含我们将通过 Nginx 提供的资产。

# Nginx 构建步骤

这一步非常简单，这是第二步也是最后一步，我们将使用官方的 Alpine Nginx 回购来获得一个安装了[反向代理](https://en.wikipedia.org/wiki/Reverse_proxy)的容器

```
FROM nginx:alpine

LABEL maintainer Eduardo Reyes <eduardo@reyes.im>

COPY ./conf/default.conf /etc/nginx/conf.d/default.conf

COPY --from=build /site/public /var/www/site

WORKDIR /var/www/site 
```

Enter fullscreen mode Exit fullscreen mode

这真的很简单，我们得到了官方的`nginx:alpine`映像，并把它作为我们的第二步构建，然后我把我的维护者标签添加到最终的容器中，在容器上复制一个 Nginx 配置文件，重要的一步就是这一步。

```
COPY --from=build /site/public /var/www/site 
```

Enter fullscreen mode Exit fullscreen mode

这一步是获取公共文件夹(我们在第一步中构建的工件)`--from`多阶段构建的`build`步骤，并将其复制到`/var/www/site`中，Nginx 将把它作为静态内容，这要感谢之前复制的基本配置文件，然后我们将目录更改到站点中，这样，如果我们想进入容器，我们就已经在代码所在的位置了。

## 最终 Dockerfile

```
FROM alpine:3.5 as build

ENV HUGO_VERSION 0.41
ENV HUGO_BINARY hugo_${HUGO_VERSION}_Linux-64bit.tar.gz

# Install Hugo
RUN set -x && \
  apk add --update wget ca-certificates && \
  wget https://github.com/spf13/hugo/releases/download/v${HUGO_VERSION}/${HUGO_BINARY} && \
  tar xzf ${HUGO_BINARY} && \
  rm -r ${HUGO_BINARY} && \
  mv hugo /usr/bin && \
  apk del wget ca-certificates && \
  rm /var/cache/apk/*

COPY ./ /site

WORKDIR /site

RUN /usr/bin/hugo

FROM nginx:alpine

LABEL maintainer Eduardo Reyes <eduardo@reyes.im>

COPY ./conf/default.conf /etc/nginx/conf.d/default.conf

COPY --from=build /site/public /var/www/site

WORKDIR /var/www/site 
```

Enter fullscreen mode Exit fullscreen mode

# 建筑形象

这是一个非常简单的 docker 命令，你应该从 Hugo 站点的根目录运行它。

`docker build -t some-hugo-site .`

要查看结果，只需:

`docker run -d -p 8080:80 some-hugo-site`

我推荐使用 [traefik](https://traefik.io/) 将容器暴露给 web，利用自动化的 let's encrypt 集成并添加一个 **http - > https** 重定向来获得一个不错的 TLS 工作流。