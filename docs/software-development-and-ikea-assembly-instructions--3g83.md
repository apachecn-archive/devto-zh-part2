# 软件开发和宜家组装说明

> 原文：<https://dev.to/danlebrero/software-development-and-ikea-assembly-instructions--3g83>

*本文原载于 [Akvo 的博客](https://akvo.org/blog/easing-open-source-contributions/)*

在 [Akvo](https://akvo.org) ，我们相信开源软件为国际发展领域的组织提供了[实质性的利益](https://akvo.org/blog/open-data-content-and-software-at-akvo/)。如今，我们期望我们使用的任何软件都具有高质量和丰富的功能。

由于开源软件，相对较小的组织和开发团队能够通过以独特或特定的方式将预先存在的开源软件组件粘合在一起，来构建复杂且功能丰富的应用程序。

能够重用所有这些组件真是太棒了，因为组装现有组件比从头开始创建它们要简单得多。

但是，这种组合可能会成为宜家最可怕的噩梦:

[![ikea instructions](img/9c962e37554cf711840fa585aa038a7a.png "Software is like IKEA")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9X0rqSPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://akvo.org/wp-content/uploads/2018/01/Ikea-instructions.png)

如果你一生中必须遵循这些指示一次，这可能不会太糟糕。但是软件开发人员必须定期遵循这些说明，因为每当我们需要添加新组件、更新这些组件的版本或构建新功能时，我们都需要重新创建、重建和重新组装我们的应用程序。

由于软件的本质，开发人员需要做出的改变(或者我们需要重新组装的部分)有时是剧烈的。打个比方，将基本的宜家书桌改造成电动可调立式书桌。

就像任何其他指令一样，总有一些东西不起作用。也许你错过了一个步骤，丢失了一个组件或者没有所有需要的工具。也许说明是错误的、误导的、含糊不清的，或者你把它们看颠倒了。

这些问题的结果都是一样的:浪费大量的时间。

## 问题与说明

多年来，IT 行业试图通过提供更准确的指令集来缓解这种痛苦，但那些指令仍然是由人类编写、阅读和解释的，我们都知道人类的交流是混乱的，远远不准确。

在 Akvo，我们希望让任何人都能轻松地为我们的开源软件做出贡献。出于这个原因，我们决定投资使我们的开源应用程序尽可能简单地组装，不是通过改进汇编指令，而是通过**完全消除它们**。

## 重新开始

由于人类的交流是不完美的，我们不得不寻找另一种工具；一个可以消除任何歧义的方法。没有什么比计算机更能逐字逐句地遵循指令了。

即使是机器盲目遵循的一套完美的指令也是不够的。计算机有积累熵的趋势，因此，即使是同一时间在同一商店购买的两台相同品牌的计算机，在使用几周后，也可能表现不同。也许当你安装 Antivirus-X 时，我将我的语言环境配置为克林贡语。这种差异足以导致汇编指令失败。

因此，除了一些机器可读的指令，我们需要一种方法来确保我们总是有一个新的开始。如果每个开发人员都从同一个点开始，那么指令将总是得到完全相同的结果。这就是软件虚拟化技术大放异彩的地方，因为它有能力在开发人员的计算机内创建虚拟计算机。简而言之，虚拟化提供了一种非常简单的方式来创造一个新的空宇宙，我们可以用它作为我们的指令集的起点。

## 码头工人平台

我们选择了 [Docker 平台](https://www.docker.com)作为虚拟化技术，使我们能够实现我们的目标，即拥有一套可重复且机器可读的指令，因此有一个一致的起点。

我们使用的 Docker 平台的两个工具是 Docker 和 Docker Compose。下面是一个你给 Docker 创建一台计算机的文件的例子:

```
FROM openjdk:8-jre-alpine

COPY target/akvo-flow-maps.jar /app/akvo-flow-maps.jar
COPY maybe-import-and-java-jar.sh /app/maybe-import-and-java-jar.sh
RUN chmod 777 /app/maybe-import-and-java-jar.sh

CMD ./maybe-import-and-java-jar.sh 
```

Enter fullscreen mode Exit fullscreen mode

这里最有趣的是第一行“FROM ”,它告诉 Docker 我们想要的起点是什么。再次感谢开源社区，在 DockerHub 上有数千个起点可供选择，这使得引导我们的定制虚拟计算机变得容易得多。其余几行是从该起点开始的一组指令。

Docker 允许我们创建虚拟计算机，Docker Compose 允许我们创建虚拟宇宙，一组计算机将生活在其中，与其他宇宙隔离。这里有一个例子:

```
services:
  postgres:
   build: 
     dockerfile: Dockerfile-postgres
   expose:
     - 5432

  flow-maps:
    build:
     dockerfile: Dockerfile-dev
    ports:
      - "47480:47480"
      - "3000:3000"

  redis:
     image: redis:3.2.9 
```

Enter fullscreen mode Exit fullscreen mode

首先要注意的是，Docker Compose 的起点总是空无一物。之后，我们指定哪些计算机应该存在于该宇宙中，在本例中，是 Postgres 数据库、Redis 数据库和 Akvo Flow Maps 应用程序。这些计算机是使用我们刚才解释的 Docker 文件创建的。

## 结果

那么将这项技术应用到 Akvo 的数据可视化和分析工具 [Akvo Lumen](https://akvo.org/products/akvo-lumen/#overview) 上的结果是什么呢？通过应用这种技术，对 Lumen 的贡献从这…到这:

[![The results](img/4af8e7fbd699a0c853f9dca93416b4cf.png "from to results")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m6M75Pdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://akvo.org/wp-content/uploads/2018/01/Lumen-open-source.gif)

git 和 docker 这两个工具，以及两个命令，使得构建和组装一个可用的 Akvo Lumen 开发环境变得极其容易。除了简化开源贡献过程之外，通过消除设置工作环境的繁重任务，我们发现使用这项技术还有两个额外的好处:

1.  它鼓励创新。由于“从零开始”极其容易，我们可以自由地试验新的组件，而不用担心损坏东西或陷入不可恢复的境地。
2.  减少浪费的时间。每当我们有一个新的团队成员，新的组件被添加到项目中，在不同的项目之间切换，或者只是一些东西停止工作，我们有两个命令来返回到一个已知的和有效的起点。

想要更好的指示？**争取无指令**。