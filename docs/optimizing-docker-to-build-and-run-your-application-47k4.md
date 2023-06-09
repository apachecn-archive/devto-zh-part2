# 优化 docker 来构建和运行您的应用程序

> 原文：<https://dev.to/marcosx/optimizing-docker-to-build-and-run-your-application-47k4>

Docker 是运行应用程序的一个很好的工具，不用担心依赖性，但是它也可以用来构建你的应用程序。在这篇文章中，我将分享一个很好的技术，我用 Docker 构建并运行了一个 web 应用程序，不再担心依赖性。

# 建筑 vs 跑步

构建一个应用程序通常需要更多的依赖，而不仅仅是运行它，大多数语言和框架都提供了一种构建应用程序的方法，将运行应用程序所需的一切捆绑在一起。

例如，Java 应用程序可以编译成一个单独的`.jar`文件，Go 应用程序可以编译成二进制文件，甚至 Ruby 应用程序也可以打包成一个 SO 包(比如 rpm)。这使得运行应用程序变得容易得多，如果使用 Docker 就更容易了。

一旦您的应用程序被打包，您可以将它复制到 docker 映像，并让 Docker 为您运行它——只需担心安装 Docker 本身。如何在 Docker 映像中实际构建整个应用程序，这样您甚至不需要担心在您的机器上(或 CI 服务器上)的依赖性？

在用于运行应用程序的同一映像上构建应用程序的主要问题是，该映像必须具备构建和运行所需的一切。

你在`Dockerfile`中添加的每一个命令都会给你的图像添加一个图层。将 Docker 映像想象成一个 git 存储库，第一个`FROM`命令是该存储库的初始提交，之后的每个命令都像一个新的提交，可能会增加存储库的大小。你在`Dockerfile`中拥有的命令越多，你的图像可能就越大。

那么，使用两个不同的图像怎么样？一个做建筑，一个做跑步？

# 建造和运行

我正在开发一个小的 web 应用程序(使用 Java 和 Spring Boot ),在搜索 docker 的最佳实践时，我看到了这个非常有用的视频。它讲述了缩小图像尺寸的不同方法(这在 Kubernetes 的上下文中特别有用)。

所以我决定应用这些技术来构建和部署这个 Java/Spring Boot web 应用程序，使用一个映像来构建`.jar`文件，另一个映像来运行它。我们在这个项目中使用了 gradle，所以我的第一步是使用 gradle 基础映像来构建应用程序。

```
FROM gradle:jdk8 AS build-env
# Setup ARGs and ENVs
# Some RUN commands
# Copy the code
COPY --chown=gradle:gradle . .
# Some more RUN commands
# Finally, build the app
RUN ["./gradlew", "build"] 
```

这张图片实际上很大。首先，它使用了一个基础映像，附带了更多的东西(与`-alpine`相比)，因为我实际上需要像`curl`这样的工具，如果我使用一个更薄的版本，我必须自己安装这些工具。

这实际上是使用构建映像的要点，您不需要担心那里有什么，因为一旦构建了最终映像，它就会被丢弃。

现在我可以定义运行应用程序的图像:

```
FROM openjdk:8-alpine
COPY entrypoint.sh entrypoint.sh
COPY --from=build-env /home/gradle/build/libs/my-app-0.0.0.jar my-app.jar
ENTRYPOINT [ "sh", "entrypoint.sh" ] 
```

这是一个非常薄的映像，它使用了`openjdk`的`8-alpine`版本，这个版本要小得多，而且做得很好，因为 Java 几乎是唯一的运行时依赖。

这两个声明都存在于同一个`Dockerfile`上，注意构建映像是如何在开头有`FROM ... AS build-env`的。这将创建一个带有`build-env alias`的临时映像，由于 Docker 只将最后一个`FROM`语句视为实际映像，因此它将被丢弃。然后在运行时映像上，它用`COPY --from=build-env`从构建映像复制。

现在，您可以使用单个`Dockerfile`构建和运行应用程序，同时针对每个上下文进行优化。使用这种模式甚至可以帮助简化您的 CI，只需在代理上安装 Docker。

感谢你花时间阅读这篇文章，希望它能帮助你改善自己的形象:)