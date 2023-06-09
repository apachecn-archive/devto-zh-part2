# 在 Docker 中运行 Expo/React Native

> 原文：<https://dev.to/ghost/running-exporeact-native-in-docker-4hme>

[![](img/062de94383a7aa9c367dfdaf41c28c51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-aiw6-I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AlnXvsEAIgA1cLp20M20Q4Q.gif) 

<figcaption>原图:[https://maraaverick . rbind . io/2017/11/docker-izing-your-work-in-r/](https://maraaverick.rbind.io/2017/11/docker-izing-your-work-in-r/)和[https://tutuappapkdownload.com/expo-apk/](https://tutuappapkdownload.com/expo-apk/)</figcaption>

在 Docker 容器中运行 Expo/React Native 有时会导致问题。在这个例子中，我将运行 Docker🐳在将在我的主机(Windows)上运行的来宾虚拟机(Ubuntu)中。我的主机还将运行另一个 VM 作为 Expo 要连接的 Android 模拟器(Genymotion)。你可以在这里，#PlugPlugPlug 获得关于如何将两个虚拟机连接在一起的更详细的帖子🔌🔌🔌。因为我已经在这两个虚拟机上设置了网络，就 Expo 而言，它也可以在主机(Windows)上运行。同样在这个例子中，我将在 Android 设备上进行测试。

### 先决条件

*   [安装对接器](https://docs.docker.com/install/)
*   [(可选)安装坞站-复合](https://docs.docker.com/compose/install/)
*   要测试的 Android 设备/仿真器

### package.json

[![](img/d9a207455c637d477e11a28b0b539b5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LEKO3CLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGYvaZ5hVpe4lQ3Rf2mq97A.png)

<figcaption>

我将在下面的例子中使用的 *package.json* 文件是一个非常简单的文件，只包括运行 Expo 所需的最少的包。

### Dockerfile

[![](img/d3a4252d4fd9d5e89fe620b871619fcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iNom9Onw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHLrfzHNuXn6BHpgmoAzHoA.png)

<figcaption>

```
FROM node:latest 
```

告诉我们使用哪个 Docker 图像作为基础，在本例中，是官方的 *node.js* 图像。这是因为它已经安装了我们需要的很多依赖项，比如 *yarn* 和 *npm* 。

```
ENV ADB\_IP="192.168.1.1"
ENV REACT\_NATIVE\_PACKAGER\_HOSTNAME="192.255.255.255" 
```

设置一个可以在 Docker 容器运行时访问的环境变量。严格地说，这些不需要在这里，因为我们总是可以在运行时注入到 Docker 容器中，但我喜欢将环境变量记录下来。

*ADB_IP* 是 Android 设备的 IP📱要连接到。*REACT _ NATIVE _ PACKAGER _ HOSTNAME*环境变量非常重要，因为它设置 Expo (cli)运行的 IP 地址，这是您的手机将尝试连接的 IP 地址。如果设置不正确，您将得到类似于图 1 的错误。您可以使用下面的命令在 Linux 上计算出正确的 IP 地址。第一个应该是主机 IP(我机器上的 192.168.27.128)。

```
hostname -I
192.168.27.128 192.168.130.128 172.17.0.1 172.19.0.1 172.18.0.1 
```

需要设置这个环境变量的原因是，默认情况下，React Native packager(expo 所依赖的)选择它在机器上看到的第一个 IP，因此您可以在您的主机上运行 Expo，但是当您在 Docker 容器中运行时，您无法连接到它，因为它试图使用 Docker IP 地址(以 172.xxx.xxx.xxx 开头的地址之一)。

```
EXPOSE 19000
EXPOSE 19001 
```

这实际上是元数据，让 Docker 容器的用户知道他们可以访问这些端口上的数据。

```
RUN apt-get update && \
 apt-get install android-tools-adb 
```

安装 Android 调试桥(ADB ),用于连接到 Android 设备并调试应用程序。

```
COPY package.json yarn.lock app.json ./
RUN yarn --network-timeout 100000 
```

将一些重要文件从主机复制到 Docker 容器。*package . JSON*和 *yarn.lock* 用于安装依赖项， *app.json* 是世博会最低要求。

```
CMD adb connect $ADB\_IP && \
 yarn run android
 # runs expo-cli start --android 
```

[![](img/077b215726580497f486f3c74e7e341c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jRMynIa---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/819/1%2AZYbIrUe9j6TmwssuPBJIXw.png) 

<figcaption>图 1:无法连接错误😢</figcaption>

### 运行 Docker

该命令在 Docker 映像第一次运行时运行，其他命令用于构建映像本身。这使用了传入 Docker 容器的环境变量，并通过$ADB_IP 连接到 Android 设备。然后运行 *package.json* 中的 *android* 命令。然后，您可以简单地运行以下命令来构建和启动 Docker 容器。

[![](img/0833bf6844953e4b179c42d90699d398.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4IEeEVKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIcXbBc7wMexVQhGkC51J9g.png) 

<figcaption>Docker 构建/运行命令</figcaption>

*   -t 用于命名图像(expo-android)
*   。告诉 Docker Docker 文件的位置(在当前目录中)
*   — env 设置 Docker 容器开始运行时使用的环境(使用这些新值覆盖 REACT_NATIVE_PACKAGER_HOSTNAME 和 ADB_IP)
*   -p 发布端口，在本例中，它将主机上的端口 19000 映射到 Docker 容器上的端口 19000(还有 19001)，因为我们需要访问端口 19000 和 19001，以便 Expo (expo-cli)可以连接到我们的 Android 设备。

### 码头工-化合物. yml

[![](img/b9c5599c5b77b85037e60cd7b5a0dbdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FA8OOA1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/790/1%2AprYM2gdRWurpCgYiI58vUA.png) 

<figcaption>码头工-化合物. yml</figcaption>

因为 expo 是用来构建手机应用程序的，Docker 不会用于生产。我更喜欢使用 docker-compose 来构建和运行，这意味着我可以运行一个简单的命令，一步完成构建和运行。quick side docker-compose 非常适合开发，尤其是当您需要运行多个 Docker 容器时，但它并不是真正为生产中使用而构建的。看看使用一个容器编排工具，比如 Kubernetes。

我还将主机上的当前目录挂载到 docker 容器上的/app/目录中，这样，主机上的任何文件更改也会在 Docker 容器中更改，而不必再次构建 Docker 容器。

[![](img/0904f95f7939a6dfc388867591a920fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OpBc1yQn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/706/1%2A6MCPmNosDhRT9oSEKfSXyQ.png)

<figcaption>docker-撰写构建/运行命令。</figcaption>

### [T1。包封/包围（动词 envelop 的简写）](#env)

[![](img/d6e7c5ecd1e003c5af604496890f4858.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCW0NdJB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AK2zO9L8rWsCtULuY3D9wrw.png) 

<figcaption>。环境</figcaption>

一个例子。用于将环境变量(使用 docker-compose)传递给 docker 容器的 env 文件。

### 附录

*   [用碳码图像](https://carbon.now.sh)
*   [码头工人解释](https://medium.freecodecamp.org/a-beginner-friendly-introduction-to-containers-vms-and-docker-79a9e3e119b)
*   [GitHub 问题围绕无法连接错误](https://github.com/react-community/create-react-native-app/issues/81)
*   [Genymotion 仿真器](https://www.genymotion.com/)
*   [GIF 覆盖图创建者](https://ezgif.com/overlay)

</figcaption>

</figcaption>