# 如何对申请进行归档？

> 原文：<https://dev.to/radiumsharma06/how-to-dockerize-an-application--1f7k>

这篇文章主要是展示我们需要遵循的基本步骤，以便基于不同的技术栈来对应用程序进行 dockerize。

让我们从 dockerizing 一个 nodejs 应用程序开始。首先，在项目的父目录中创建一个名为“Dockerfile”的文件，不带任何扩展名。

这是一个基本的 dockerize 文件，我们需要 dockerize 一个节点应用程序

```
FROM node:4-onbuild
RUN mkdir /app
COPY . /app/
WORKDIR /app
RUN npm install
EXPOSE 8234
CMD [ "npm", "start" ] 
```

Enter fullscreen mode Exit fullscreen mode

我将解释每个命令及其作用。如果你仔细观察，你会注意到这个 docker 文件实际上在做我们运行任何 nodejs 应用程序所做的基本步骤。

让我们逐一查看每个命令。

# 1。从节点:4-onbuild

该命令从 docker hub(docker 映像的公共中心)获取/下载基础映像。要运行节点应用程序，您需要在系统中安装节点，或者要运行 java，您需要 jdk，就像我们需要在 docker 环境中添加基本节点映像一样。

# 2。运行 mkdir /app

在这个命令中，我们创建一个空目录，这个目录将是我们的代码文件的工作目录。

# 3。收到。/app/

该命令将当前目录中的所有文件复制到新创建的 app 目录中。您的 docker 文件应该位于项目的父目录中。

# 4\. WORKDIR /app

在这里，我们从当前目录切换到应用程序目录，在那里我们将运行我们的应用程序。

# 5。运行 npm 安装

此 npm 命令与节点应用程序相关。当我们在步骤 3 中复制所有依赖项时，我们的主文件 package.json 应该已经被复制了。因此，运行上面的命令会安装文件中的所有依赖项，并使用提到的节点包创建一个 node_modules 文件夹。

# 6。暴露 8234

这个命令公开了一个我们希望 docker 映像运行的端口。

# 7。CMD [ "npm "，" start" ]

这是运行节点应用程序的命令行操作。它可能因项目而异。

现在，一旦我们准备好了 docker 文件，让我们用它来创建一个图像。假设您的系统上安装了 docker，让我们遵循一些简单的步骤:-

*   导航到包含 Dockerfile 的目录。

*   在终端上运行以下命令:-

    ```
    docker build -t 'any image name' . 
    ```

    这个命令将开始逐个执行 Dockerfile 中的步骤。它将首先在您的本地 docker 存储库中寻找基本映像，如果找不到，它将从 docker hub 下载映像。
    考虑到 Dockerfile 编写正确，您的形象将被建立。

*   运行以下命令来查找您的映像。

    ```
    docker images 
    ```

    该命令将列出本地 docker repo 中的所有图像。考虑到您将您的图像名称命名为 hello-docker，您可以在运行上面的命令时看到这个名称。

*   现在我们已经准备好了图像，但是我们需要一个容器来查看我们的应用程序的运行。容器就是你的图像的一个运行实例。运行以下命令启动映像或构建容器。

    ```
    docker run -p 8080:8080 'your image name' 
    ```

    这将创建一个包含您的图像的容器。您可以给出自己选择的端口号。

*   现在，在运行了前面的命令之后，我们可以转到提到的端口，查看我们的应用程序运行情况。要查看正在运行的容器，请运行以下命令:-

    ```
    docker ps 
    ```

    要查看 docker 中的所有容器，请运行以下命令:-

    ```
    docker ps -a 
    ```

    您将看到带有随机名称和 id 的容器。

*   要删除容器，请运行以下命令:-

    ```
    docker rm 'container name/ container id' 
    ```

*   要停止容器运行，请执行以下操作:-

    ```
    docker stop 'container name/ container id' 
    ```

*   要启动容器，请运行以下命令:-

    ```
    docker start '<container name/ container id>' 
    ```

*   您也可以删除图像，但要确定它没有任何容器。运行以下命令:-

    ```
    docker rmi 'your image name' 
    ```

希望这篇文章有助于 docker 化一个基本的应用程序，我们可以用 docker 做更多的事情。

下面是我为一个 java 项目创建的另一个 docker 文件。你可以很容易地把它和以前的 Dockerfile 文件联系起来。

```
FROM openjdk:8-jdk-alpine
RUN mkdir /apollo-services
COPY . /app
WORKDIR /app
RUN ./gradlew raml-generate
RUN ./gradlew clean build
WORKDIR /app/service-impl/build/libs
EXPOSE 8080
ENTRYPOINT ["java","-jar","main.jar"] 
```

Enter fullscreen mode Exit fullscreen mode

谢谢大家，祝大家有美好的一天:)！！