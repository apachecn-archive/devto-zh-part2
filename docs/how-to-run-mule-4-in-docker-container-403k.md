# 如何在 Docker 容器中运行 Mule 4

> 原文：<https://dev.to/manikmagar/how-to-run-mule-4-in-docker-container-403k>

最近我更新了 javastreets/mule docker 镜像到 Mule ESB 4.1.0 社区版。在这篇文章中，我们将在 Docker 容器中运行一个 Hello Mule 4 应用程序。

目录

*   1.什么是骡子码头工人形象？
*   2.向 Mule 4 应用程序问好
*   3\. Dockerfile
*   4.构建并运行
*   5.结论

## 1。什么是骡子码头工人形象？

如果你正在寻找 Mule Docker 图片，那么你可以在 [docker hub](https://hub.docker.com/r/javastreets/mule/tags/) 上查看 javastreets/mule docker 图片。这可能是最小和最紧凑的 Mule ESB 映像。

如果您的机器上安装了 Docker，您可以将图像拉成`docker pull javastreets/mule`。

## 2。向 Mule 4 应用程序问好

我在 github 的 [mule4-examples](https://github.com/manikmagar/mule4-examples/tree/master/say-hello-mule4-docker) 库中添加了一个简单的 Hello Mule 4 应用程序。这个应用程序只有一个 HTTP 侦听器流，它侦听[HTTP://localhost:8081/test/hello](http://localhost:8081/test/hello)。

它是为当前 **Mule 4 社区版运行时**打造的。

## 3。Dockerfile 文件

将我们的应用程序构建并打包到 mule 的 docker 文件

```
FROM javastreets/mule:latest-4

COPY ./target/say-hello-mule4-docker*.jar /opt/mule/apps/

CMD ["/opt/mule/bin/mule"] 
```

## 4。构建并运行

构建并运行应用程序——执行`sh buildAndRun.sh`。

这执行 3 个步骤-

*   运行 Maven Build 来打包应用程序。
*   构建名为 hellomule4 的 docker 映像，并将该应用程序添加到 Docker 中的 mule。
*   启动 docker 容器在前台运行 mule。(按 CTRL+C 停止骡子)

要在后台运行应用程序，您也可以运行以下命令-

`docker run -d --name hellomule4 -p 8081:8081 hellomule4`

这个 docker 映像公开了 8081 端口，并将其绑定到 localhost 的 8081。

一旦应用程序运行，访问下面的网址看到你好，从骡子-

[http://localhost:8081/test/hello](http://localhost:8081/test/hello)

它应该输出-

*来自 Mule ESB 的你好(版本:4.1.0)。我正在运行 docker image javastreets/mule:latest-4*

## 5。结论

在 Docker 容器中运行 Mule 应用程序很容易。javastreet/mule docker image 提供了一个简单的方法让 mule 开始工作！Github 上的示例源代码可从这里获得- [mule4-examples](https://github.com/manikmagar/mule4-examples/tree/master/say-hello-mule4-docker)