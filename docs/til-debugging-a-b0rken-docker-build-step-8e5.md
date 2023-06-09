# TIL:调试一个 b0rken Docker 构建步骤

> 原文：<https://dev.to/jonasbn/til-debugging-a-b0rken-docker-build-step-8e5>

# 调试一个 b0rken Docker 构建步骤

`docker`太棒了！-但是如果你的建造不成功，那会很痛苦。

这个漂亮的小技巧可以用来检查一个图层播放不好的图像，你可以访问容器中的某种外壳。

我用 shell 内置命令`false`模拟这一点，但是它可以是任何构建步骤，其中容器的完整性依赖于构建步骤的成功

```
# Dockerfile
FROM alpine:latest

LABEL maintainer="jonasbn"
RUN echo "hello world" > /tmp/hello_world
ENTRYPOINT ["cat", "/tmp/hello_world"] 
```

Enter fullscreen mode Exit fullscreen mode

建造它

```
$ docker build -t smelly_container . 
```

Enter fullscreen mode Exit fullscreen mode

因此，为了跳过这个糟糕的步骤，您可以将:`; exit 0`添加到*困难的* `RUN`步骤中，并构建与前面 shell 命令的返回值
无关的内容

```
# Dockerfile
FROM alpine:latest

LABEL maintainer="jonasbn"
RUN echo "hello world" > /tmp/hello_world ; false; exit 0
ENTRYPOINT ["cat", "/tmp/hello_world"] 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以检查集装箱

```
$ docker run -it --entrypoint /bin/sh smelly_container
/ # cat /tmp/hello_world 
hello world 
```

Enter fullscreen mode Exit fullscreen mode

你当然可以跑，你的里程数可能会根据你的*难度*构建步骤的*难度*的严重程度而变化，我们选择忽略

```
$ docker run -it  smelly_container
hello world 
```

Enter fullscreen mode Exit fullscreen mode

来源: [StackOverflow](https://stackoverflow.com/questions/30716937/dockerfile-build-possible-to-ignore-error)

最初发布于[我的 TIL 收藏](https://github.com/jonasbn/til)