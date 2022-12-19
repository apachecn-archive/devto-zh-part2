# Docker 和图像尺寸

> 原文：<https://dev.to/pzelnip/docker-and-image-sizes-209a>

*本文最初发布于:[https://www.codependentcodr.com/docker-and-image-sizes.html](https://www.codependentcodr.com/docker-and-image-sizes.html)2018 年 8 月 12 日*

所以这让我很惊讶。我有一个 docker 文件，看起来基本上是这样的:

```
FROM alpine:latest

RUN apk add --no-cache --update \
    python3 nodejs-current-npm make git curl

RUN python3 -m ensurepip
RUN pip3 install --upgrade pip

RUN npm install -g markdownlint-cli

# needed for one of the packages in requirements.txt
RUN apk add --no-cache --update python3-dev gcc build-base

COPY requirements.txt /build/requirements.txt
RUN pip3 install -r /build/requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

构建此映像的结果是`docker images`报告的映像大小为 379MB。有点大，所以我想修剪一下。

因为在将`requirements.txt`复制到映像之前安装的那些包只是为了能够*安装*一个包，所以它们没有理由留在映像中。酷，这样我们就可以用草皮来节省图像大小:

```
FROM alpine:latest

RUN apk add --no-cache --update \
    python3 nodejs-current-npm make git curl

RUN python3 -m ensurepip
RUN pip3 install --upgrade pip

RUN npm install -g markdownlint-cli

# needed for one of the packages in requirements.txt
RUN apk add --no-cache --update python3-dev gcc build-base

COPY requirements.txt /build/requirements.txt
RUN pip3 install -r /build/requirements.txt

# cleanup unneeded dependencies
RUN apk del python3-dev gcc build-base 
```

Enter fullscreen mode Exit fullscreen mode

很好，这导致图像大小为 381MB，节省了 2MB(T3)。等待....瓦特？

[![<INSERT ALT TEXT HERE>](../Images/915c83f158319fac6adc6924f3e39d15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--13Cak3dZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codependentcodr.com/static/imgs/wat.jpg)

所以我*删除了*一些东西，最后得到了一个几 MB 的*大*的图像？这是怎么回事？

如果我们想了解技术，我们就从这里开始讨论 Docker 如何使用分层文件系统，这样一来(与 Git repos 并不完全不同),一旦某些东西被添加到映像中，它就不能真正地(或者至少不容易地)被删除。

看到这期提到我正在谈论的:[https://github.com/gliderlabs/docker-alpine/issues/45](https://github.com/gliderlabs/docker-alpine/issues/45)

那我们该怎么办？嗯，我们*将*操作组合成一个单一的 Docker 指令:

```
FROM alpine:latest

RUN apk add --no-cache --update \
    python3 nodejs-current-npm make git curl

RUN python3 -m ensurepip
RUN pip3 install --upgrade pip

RUN npm install -g markdownlint-cli

COPY requirements.txt /build/requirements.txt

RUN apk add --no-cache --update python3-dev gcc build-base && \
    pip3 install -r /build/requirements.txt && \
    apk del python3-dev gcc build-base 
```

Enter fullscreen mode Exit fullscreen mode

因为添加和删除 apk 包是一条 Docker 指令，它们不会增加构建映像的大小(你可以把层看作是`Dockerfile`中每条指令后的“检查点”)。

有了这个改变，我的图像尺寸明显减小了。多少钱？让工具告诉我们:

```
$ docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
someimage                    combineops          1744771da3fa        About a minute ago   216MB
someimage                    removepckgs         fc5877e2afad        4 minutes ago        381MB
someimage                    original            b6e5e43b22e0        5 minutes ago        379MB 
```

Enter fullscreen mode Exit fullscreen mode

也就是从 *379MB* 降到了 *216MB* 。一点也不差。

这是一个经典的“时间与空间”的权衡。因为我不得不将`requirements.txt`行上移，这意味着这个映像的构建通常较慢(因为 Docker 缓存的工作方式，如果我更改 requirements.txt 文件，那么它将不得不在`requirements.txt`更改时安装那些 apk 包)。但是，我认为节省的空间(40%+)是值得的。