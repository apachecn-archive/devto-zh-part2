# Docker 命令—终极备忘单

> 原文：<https://dev.to/nickparsons/docker-commands-the-ultimate-cheat-sheet-33n2>

[![](../Images/08edc19c5965b298a6bc70076a1c0162.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S7vrEZM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZtB3bSD2qOkRT3xJ_5gDQg.jpeg)

如果您还不知道，Docker 是一个使用“容器化”构建分布式软件的开源平台，它将应用程序与它们的环境打包在一起，使它们更易于移植和部署。

由于其强大的功能和生产力，Docker 已经成为软件开发团队非常流行的技术。然而，这种能力有时会让新用户很难进入 Docker 生态系统，甚至让有经验的用户很难记住正确的命令。

幸运的是，有了正确的学习工具，你就可以更轻松地开始使用 Docker。本文将是 Docker 的一站式商店，介绍一些最佳实践和任何用户都应该知道的必备命令。

### Docker 命令和最佳实践

在我们进入使用 Docker 的最佳实践之前，这里有一个你应该知道的词汇的快速概述:

*   **层**:一组只读文件或命令，描述如何在容器下设置底层系统。各层构建在彼此之上，每一层都代表文件系统的一个变化。
*   **Image** :一个不可变的层，它构成了容器的基础。
*   **容器**:可以作为独立应用程序执行的图像实例。容器有一个可变层，它位于图像之上，并与底层分离。
*   **Registry** :用于分发 Docker 映像的存储和内容交付系统。
*   **存储库**:相关 Docker 映像的集合，通常是同一应用程序的不同版本。

#### **记住这一点，这里有一些用 Docker 构建应用程序的快速技巧:**

*   试着让你的图片尽可能的小。这将使它们更容易传输，并在启动新容器时更快地加载到内存中。不要包含库和依赖项，除非它们是应用程序运行的绝对要求。
*   如果您的应用程序需要可伸缩性，可以考虑使用 Docker Swarm，这是一个将节点集群作为单个虚拟系统进行管理的工具。
*   为了最大化效率，将 Docker 与持续集成和持续部署实践结合使用。您可以使用诸如 [Docker Cloud](https://docs.docker.com/docker-cloud/builds/automated-build/) 之类的服务从源代码中自动构建图像，并将它们推送到 Docker 存储库。

### 下面的  中，您将找到开始使用容器所需的所有基本 Docker 命令:

#### **用 Docker 容器开发**:

*   **docker Create【image】**:从一个特定的图像创建一个新的容器。
*   **docker 登录**:登录 Docker Hub 库。
*   **docker pull【图片】**:从 [Docker Hub 库](https://hub.docker.com/)中拉一张图片。
*   **docker 推送【用户名/图片】**:将图片推送至 Docker Hub 库。
*   **Docker Search【term】**:在 Docker Hub 知识库中搜索特定的术语。
*   **docker tag[source][target]**:创建引用源图像的目标标签或别名。

#### **运行码头集装箱**

*   **docker start【容器】**:启动一个特定的容器。
*   **停靠站【集装箱】**:停靠特定的集装箱。
*   **docker exec-ti[container][command]**:在特定容器内运行 shell 命令。
*   **docker run-ti—image[image][container][command]**:同时创建并启动一个容器，然后在里面运行一个命令。
*   **docker run-ti—RM—image[image][container][command]**:同时创建并启动一个容器，在里面运行一个命令，执行完命令后移除容器。
*   docker pause [container]:暂停特定容器中运行的所有进程。

#### **使用 Docker 实用程序:**

*   **docker history [image]** :显示特定图像的历史。
*   **docker images** :列出当前存储在系统上的所有图像。
*   **docker inspect [object]:** 显示特定 docker 对象的底层信息。
*   **docker ps** :列出当前运行的所有容器。
*   **docker 版本**:显示系统当前安装的 docker 版本。

#### **清理你的 Docker 环境:**

*   **docker kill【集装箱】**:杀死一个特定的集装箱。
*   **docker kill $(docker ps -q)** :杀死当前正在运行的所有容器。
*   **docker rm [container]** :删除当前没有运行的特定容器。
*   **docker rm $(docker ps -a -q)** :删除当前没有运行的所有容器。

希望这份指南能成为你的 Docker 备忘单。如果我错过了什么，请告诉我，我会很高兴地补充。

**快乐编码**！✌

* * *