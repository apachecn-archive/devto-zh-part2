# 针对工作负载优化芹菜

> 原文：<https://dev.to/japneet121/optimizing-celery-for-workloads-57i9>

## 介绍

Firstly, a brief background about myself. I am working as a Software Engineer in one of the Alternate Asset Management Organization (*Handling 1.4 Trillion with our product suite*) responsible for maintaining and developing a product **ALT Data Analyzer.** My work is focused on making the engine run and feed the machines with their food. This article explains the problems we faced with scaling up our architecture and solution we followed.  I am dividing the blog in the following different sections:

*   产品简介
*   当前架构
*   数据负载问题和不眠之夜
*   尝试过的解决方案
*   最终目的地

## 产品简介

构建这个产品的想法是给用户一个公司周围网络的聚合视图。我所说的网络是指在互联网的所有分散节点上自由流动的数据。我们试图捕捉公司的所有财务、技术和基本面数据，通过我们的按摩和分析管道传递这些数据，并在这些数据的基础上提供一个聚合视图。我们的仪表板可以是公司所有信息的一站式平台，可以帮助投资者进行分析。

到目前为止，我们支持的一些数据源有:

*   多种来源的股票新闻
*   简单讯息聚合订阅
*   推特
*   Reddit
*   雅虎新闻
*   雅虎财经
*   收入日历
*   报告归档
*   公司财务
*   股票价格

## 当前架构

[![](../Images/0ad1c41a6f5a8e62b3e9586167206b71.png)](https://4.bp.blogspot.com/-zrXUbB9-EVA/WvaSLHGo0fI/AAAAAAAAbmU/jwHR-jSLH7MALNt8Q7ghWotC5H9dhQvcgCLcBGAs/s1600/internet.jpeg)

### 广阔的视野

We knew that problem that we are solving has to deal with the cruel decentralized Internet. And we need to divide the large task of getting the data from the web and analyzing it into small tasks. [![](../Images/9b4b624764b0aa6446e2cd3b17e177e9.png)](https://1.bp.blogspot.com/-tPW3nCkXYps/WvaVW4XlHoI/AAAAAAAAbmg/_jpZxvZMOA8M-CpZ0fuffHzeU9IzaYbFgCLcBGAs/s1600/croped1.jpeg) Fig 1 On exploring different projects and technologies and analyzing the community support we came to a decision to use **Python** as our language of choice and **Celery** as our commander.  Python is a pretty vast language backed by a large set of libraries. Since inception, It has gained a lot of popularity among the developer and Data Science communities. One of the major reason to use python as a backend is the project Celery.  Its website defines celery as*An asynchronous task queue/job queue based on distributed message passing. It is focused on real-time operation, but supports scheduling as well.* To more about celery you can visit its website [here](http://www.celeryproject.org/). By now we were clear of how we want to proceed. We wanted to divide the process in Fig 1 into granular units (in celery terminology task). Keeping this as the baseline we identified all the individual units which can work independently in the system. This gave rise to a new look to Fig 1 [![](../Images/b6558694463a506e0897ab273107ce01.png)](https://2.bp.blogspot.com/-ShzlN3uYpA4/Wvacz_r0wtI/AAAAAAAAbmw/2q0sPcEN4FM3qTkEPOMTqq_zQyL5CID8wCLcBGAs/s1600/cropped1.jpeg) Fig 2 Were are using MongoDB in a replication cluster as a Database engine and Redis DB for queuing and communication between independent celery workers. The following figure describes the communication of celery works with the help of broker(Redis) [![](../Images/c6966b4d676182b6592cef70b49eb98a.png)](https://3.bp.blogspot.com/-S1r1aSUiOcc/Wvae5cP0BqI/AAAAAAAAbnE/QNNKir9dgUEGEpDk00KWfOOqLfVxKlAoQCLcBGAs/s1600/workers.jpeg)

## 数据负载问题和不眠之夜

开发工作负载一切正常，我们在 celery 集群中部署了大约 5 台机器(i7 处理器，每台 16g 内存)，每台机器运行一些 celery workers。

但这是暴风雨前的平静。当我们计划增加我们的工作负载和配置更多的任务时，系统开始变弱并频繁地耗尽内存。我们现在每天生成大约 1000 万个任务。Celery workers 无法以这样的速度处理任务，这使得队列中的任务越来越多，降低了 Redis broker 的速度，因为它现在存储了大量的任务，这反过来又使整个系统耗尽了内存并逐渐停止运行。

[![](../Images/5e541e3a8a1afe587aaf4146f97b58d5.png)T2】](https://3.bp.blogspot.com/-yFpJdkfSkB0/WvgHuFRr77I/AAAAAAAAbno/p4siRfrXTVIDEypgbo5qd9LfiKRjoW-qACLcBGAs/s1600/calm.jpg)

我们设计精美的船正在下沉，我们必须做些什么来拯救它。

 T2】

## 尝试过的解决方案

当我们面对这个问题时，我们想到的第一个方法是向集群中添加更多的节点，其他任何开发人员都会采用这种方法。在讨论该解决方案时，有一个争论，即如果负载随着时间的推移再次增加，我们是否会一次又一次地添加更多的节点。

所以，我们跳过这个解决方案，同意深入挖掘问题。

通过深入分析，我们意识到大量任务由发布者填充，这些任务存储在 Redis 队列中，随着数据加载，Redis 最终会降低任务发送给消费者的速度，使消费者闲置。

所以我们的系统陷入了无限循环。

*   Redis 以高速发送任务，但消费者无法以这样的速度消费。
*   Redis 降低了发送任务的速度，现在消费者大部分时间都处于闲置状态。

在这两种情况下，有疑问的通用名称是 Redis DB。在研究和探索 RedisDB 时，我们发现它是一个单线程系统，以循环方式执行所有任务。此外，将系统收到的任务保存在内存中会增加内存消耗，并且在繁重的负载下，单线程会忙于执行持久性和其他任务，这会减慢消费者发起的轮询过程。所以，我们找到了我们面临的两个问题的原因。

为了处理繁重的工作负载，选择之一是将 Redis 服务器分成一个集群。于是我们创建了一个 Redis 分区集(教程可以看[这里](https://redis.io/topics/partitioning))。我们创建了一个由三个节点组成的集群，每个节点通过一种*一致散列技术*处理相同数量的密钥。

我们用 celery 应用程序插入这个集群，工作人员抛出一个异常“*移动到服务器 192.168.12.12* ”

[![](../Images/f0300f2da1d16eae653a26834c03e5ec.png)T2】](https://2.bp.blogspot.com/-zw8o3gX19lI/WvibxsT5RII/AAAAAAAAboE/nEeZLU_ia0kqszxTpylxt2FoYDDyfrKfgCEwYBhgL/s1600/fr_201691716455.jpg)

在谷歌上，我们发现芹菜还不支持 Redis 集群。一方面，我们认为我们有一个解决方案，但另一方面，它还没有得到底层框架的支持:(

现在，探索再次开始，以获得我们问题的解决方案，我们想到在 Redis 集群[twembroxy](https://github.com/twitter/twemproxy)前使用代理服务器。但是这次我们首先选择检查与我们的框架和 boom 的兼容性......我们走这条路再明智不过了。

芹菜尚未支持代理。

由于对这个不兼容的问题感到沮丧，我们试图找出所有的东西都与框架兼容。一些兼容的经纪人是

*   SQS
*   雷迪斯
*   兔子 q
*   动物园管理员

一个直接的想法是尝试不同的经纪人，所以我们开始探索这些选项。下表证明有助于缩小我们的研究范围

<colgroup><col width="23%"> <col width="22%"> <col width="25%"> <col width="31%"></colgroup> 
| **名称** | **状态** | **监控** | **遥控器** |
| *RabbitMQ* | 稳定的 | 是 | 是 |
| *再说一遍* | 稳定的 | 是 | 是 |
| *亚马逊 SQS* | 稳定的 | 不 | 不 |
| 动物园管理员 | 实验的 | 不 | 不 |

Redis 是我们已经尝试过的，所以我们选择了第二个选项，它是稳定的，提供了更多的功能，即 RabbitMQ。

**剧透:**

到目前为止，我们知道 RabbitMQ 是代理的最佳选择之一，在生产中被各种各样的客户使用，Redis 是结果后端(由任务存储在 Celery chains 和 chords 中的中间结果)的最佳选择。

我们对系统进行了必要的修改，并尝试运行该系统。

[![](../Images/1ff9e303a2d483748954cc8cd9718a9e.png)T2】](https://3.bp.blogspot.com/-dTzKGioKH8c/Wvig1fw85LI/AAAAAAAAboU/3bugIbOyg6IrPyDrVIVrGqMfr6DC-tJ9QCLcBGAs/s1600/fc6ba9d5149c057a2582a7f07523abd2.jpg)

这一次系统几乎进入一个不可恢复的状态，消耗所有的内存，我们不得不重新启动服务器。

当这一切发生的时候，我们通过管理工具监控 RabbitMQ，发现了一件可疑的事情。它创建了大量几乎与任务数量一样多的队列。

在 googling 上，我们发现创建队列是为了存储中间任务的结果，这消耗了太多的内存空间，因为所有这些存储都在内存中。

 T2】

## 最终目的地

从我们的探索中收集了所有的线索，我们决定使用 Redis 和 RabbitMQ 这两个系统来完成他们最擅长的工作。

我们部署 Redis 作为结果后端来存储中间结果，部署 RabbitMQ 作为代理来维护通信和传递任务(记住上面的剧透)。

[![](../Images/b7c496122ef2d7f040e38c738a6e9c83.png)T2】](https://1.bp.blogspot.com/-zFlxvWKDZh8/WvijzlNdrfI/AAAAAAAAbog/ZOD6D9Hq9_8i5wL5zq3BK60BCfqCR-bSQCLcBGAs/s1600/fg.jpeg)

借助这种架构，我们能够在每天超过 1000 万个任务的工作负载下运行系统，并且可以轻松扩展。

希望这有助于经历同样问题的人。

## 承认

我们从芹菜文档、Redis 文档、Stackoverflow 线程、芹菜的 Github 问题页面、Instagram 和 Trivago 的案例研究中得到启示。