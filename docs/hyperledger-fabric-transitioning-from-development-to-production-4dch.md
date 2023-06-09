# Hyperledger Fabric:从开发过渡到生产

> 原文：<https://dev.to/presto412/hyperledger-fabric-transitioning-from-development-to-production-4dch>

您已经建立了您的开发环境。设计了你的链码。设置客户端。也制作了一些看起来不错的用户界面。本地一切正常。所有的测试都通过了，所有的服务都启动并运行了。

所有这些，你还是不满意。你想做得更多。你想扩大网络规模。模拟生产级环境。也许您甚至想部署到生产环境中。你试一试。添加多个订购者。添加更多拥有自己的同行和 ca 的组织。你试着用多台机器测试它。

[![](img/b4218be182ceede936bf479bbd58de24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j04sqTEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/51oEcOu.jpg)

这就是你所做的一切失败的地方，不管你做了什么。你以某种方式调试了所有的东西，并想出了一些你并不完全引以为豪的巧妙方法。有些部分可以，有些不行。你结束了一天，仍然不满意。

我不知道你是否能理解。我经历了这个阶段，绞尽脑汁，喝了多杯咖啡试图理清思路，从头开始调试。多面手掌和“啊，这是如何工作的”后来，我决定写这篇文章，并成为你的友好的邻里开发者-人(对不起)。

## 第一步

Fabric 为网络提供了两个共识协议——Solo 和 Kafka——Zookeeper。
如果你只在独奏模式下工作(可在`configtx.yaml`中配置)，这是你改变它的地方。当我们希望我们的网络在 3 个以上的对等点之间共享时，在一个特定的、被诅咒的节点出现故障的情况下，拥有多个排序器是有意义的。

按照现在的设计，订购者就像是织物网络中的邮递员。它们获取事务并将其转发给对等体。Solo 模式要求我们所有的订购者都必须正常工作，如果一个订购者出现故障，整个网络都会瘫痪。因此，这里的诀窍是使用基于卡夫卡的订购服务。我已经在这里全面解释了它在织物[中的工作原理。](https://codeburst.io/the-abcs-of-kafka-in-hyperledger-fabric-81e6dc18da56)

Fabric [docs](http://hyperledger-fabric.readthedocs.io/en/latest/kafka.html) 提供了一些值得遵循的最佳实践。

所以这是我们的第一步——走向卡夫卡。不要忘记在您的事务配置文件中指定代理。下面提供了一个示例。

config tx . YAML〔t0〕

```
 ...
    Orderer: &OrdererDefaults
        OrdererType: kafka
        Addresses:
            - orderer0.example.com:7050
            - orderer1.example.com:7050
            - orderer2.example.com:7050
            - orderer3.example.com:7050
        BatchTimeout: 2s
        BatchSize:
            MaxMessageCount: 10
            AbsoluteMaxBytes: 99 MB
            PreferredMaxBytes: 512 KB

        Kafka:
            Brokers:
                - kafka0:9092
                - kafka1:9092
                - kafka2:9092
                - kafka3:9092
    ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经解决了订购者的依赖性，让我们深入一些网络级别的技巧。

[![](img/0eea69626a76190431dde927a215fd2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X5sgxgVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://edenmal.moe/post/2016/Docker-for-Admins-Workshop-v2/network-stuff-brace-yourself-meme.png)

## 一窝蜂的服务

我们用于 Hyperledger Fabric 的所有映像都是 docker 映像，我们部署的服务都是 docker 化的。要部署到生产环境，我们有两个选择。嗯，我们有很多选择，但我只会描述那些不会让你被解雇的选择。暂时如此。

这两个选择是 Kubernetes 和 Docker Swarm。我决定继续使用 Docker Swarm，因为我真的不喜欢为前者设置的 hacky docker-in-docker。更多信息请点击这里。

### 什么是蜂群？

从 Docker 文档中，

“群由多个 Docker 主机组成，这些主机以群模式运行，充当管理器(管理成员资格和委托)和工作器(运行群服务)。一个给定的对接主机可以是一个经理，一个工人，或两者兼而有之”。

这是一种集群管理和编排方法，是 Docker 引擎 1.12 的特色。

请考虑一下——区块链管理员可以访问这些经理节点，每个潜在的组织都可以成为指定的员工节点。这样，工作节点将只能访问允许的资源，不会有任何阻碍。Swarm 使用 raft 共识算法，管理人员和工作人员适应服务的复制，从而提供崩溃容忍度。

### DNS -不要忘记给定主机名

不给你的服务提供主机名是一个新手的错误。每个服务的主机名都是必需的。这就是 Swarm 中的一个服务将 IP 地址映射到主机名的方式。这广泛用于动物园管理员合奏。

考虑 3 个 Zookeeper 实例。我们会让这些相互依赖以实现同步。当这些被部署到一个群时，这个群给出一个内部 IP 地址，这是非常动态的。

```
 ...
      zookeeper0:
        hostname: zookeeper0.example.com
        image: hyperledger/fabric-zookeeper
        environment:
          - ZOO_SERVERS=server.1=zookeeper0:2888:3888 server.2=zookeeper1:2888:3888 server.3=zookeeper2:2888:3888
    ... 
```

Enter fullscreen mode Exit fullscreen mode

另一个依赖于此的 Zookeeper 实例，

```
 ...
      zookeeper1:
        hostname: zookeeper1.example.com
        environment:
          - ZOO_SERVERS=server.1=zookeeper0:2888:3888 server.2=zookeeper1:2888:3888 server.3=zookeeper2:2888:3888
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 限制和约束

[![](img/bdfb4be74dc3562e5d819a13278d2392.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E0FtN5Jr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/516JMJ94%252BqL._SX425_.jpg)

通常，在一个群中，服务被默认分配给任何节点。Docker Swarm 将始终努力提高管理器节点上的性能。因此，它会尝试将服务分发到工作节点。当一个工作节点关闭时，管理器可以在集群内部重新分配服务。

现在，有一些非常重要的服务，我们希望保持运转，比如 Kafka-Zookeeper ensemble，因为它们在所有订购者之间同步交易。因此，我们在这里想要做的是确保我们不会遭受任何停机时间。可能还有一个保存证书的服务，重要的是证书不能在群网络中泄露。因此，我们需要对集群中的堆栈部署进行限制。

我们可以限制部署到群中节点的服务。下面是一个简单的例子。

```
 ...
    services:

        peer0:
            deploy:
                replicas: 1
                restart_policy:
                    condition: on-failure
                    delay: 5s
                    max_attempts: 3
                placement:
                    constraints:
                        - node.role == Manager
                        - node.hostname == Skcript  
    ... 
```

Enter fullscreen mode Exit fullscreen mode

请注意，deploy 部分还定义了崩溃处理，因此使用它对您有好处。当我们实现约束时，几乎每个认证问题都可以解决。
您可能会注意到这违背了 docker Swarm 的目的，它应该尽可能地维护群体的状态。如果是这种情况，您将不得不在另一个可以处理停机时间的节点上花费更多，并且可能会扩展您的约束。

### 保护证书和密文

[![](img/f847bb01cf4e4ed6c525a57a66bf083c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IvxUbflb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://info.hummingbirdnetworks.com/hs-fs/hubimg/Darth.Vader_.I.Find_.Your_.Lack_.Of_.Cyber_.Security.Disturbing.MEME_1.jpg%3Ft%3D1527893520042%26width%3D600%26name%3DDarth.Vader_.I.Find_.Your_.Lack_.Of_.Cyber_.Security.Disturbing.MEME_1.jpg)

证书不应该向网络中的其他实体公开。理想情况下，最简单的做法是将特定组织的证书提供给托管它的节点，并安装到 docker 容器和简单 linux 机器共有的位置，如`/var/<network-name>/certs`。这样，您可以只装载所需的卷。
说到卷，挂载的时候一定要有绝对路径。您只能从管理节点将服务部署到 Docker Swarm，因此它需要在托管它的节点中拥有证书，否则服务将关闭。

一个例子:

坞站-组合-同级. yml〔t0〕

```
 ...
    volumes:
                - /var/run/:/host/var/run/
                - /var/network/certs/crypto-config/peerOrganizations/state.example.com/peers/peer0.org1.example.com/msp:/var/hyperledger/msp
    .... 
```

Enter fullscreen mode Exit fullscreen mode

在部署服务之前，应该在主机工作节点中复制`/var/network/certs/`目录。

我不推荐任何人都可以访问的、漂浮在群体中的独立服务。

命名服务

`docker stack deploy`不允许某些字符用于服务创建。如果你直接从教程开始工作，你将会有像`peer0.org1.example.com`、`orderer0.example.com`这样的服务名。

所以最好把它们命名为`peer0_org1`，和`orderer0`等等。

### 抓取人名

[![](img/8ff61820a550efecdbcd11b2acaee414.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a5FHwPM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://78.media.tumblr.com/824637154a6cb88c100bf37c6caa086d/tumblr_no1fm1DSou1qjnl39o1_500.gif)

Docker Swarm 通常在服务的栈名前面加上前缀，后缀是 SHA256 哈希。因此，为了执行任何命令，我们需要服务的名称，由 Swarm 提供给它们。
举例来说，如果你将你的服务命名为`peer0_org1`，并且你将它部署到的堆栈是`deadpool`，那么 swarm 将会给它起一个类似于`deadpool_peer0_org1.1.sa213adsdaa….`的名字。

您可以通过一个简单的命令
获取它的名称

```
 docker ps --format="{{.Names}}" | grep peer0_org1 
```

Enter fullscreen mode Exit fullscreen mode

专业提示:环境变量是你最好的朋友。一定要为你所有的脚本准备一个专用的`.env`。

## 频道和链码

当您有多个组织，并且您希望在这些组织之间运行自定义通道，并在每个通道上安装不同的智能合约时，这就是它应该如何工作。

*   必须根据 configtx.yaml 中定义的内容创建通道。您创建通道，并加入各自的对等体。一个示例通道创建命令如下所示:

```
 docker exec -e "CORE_PEER_LOCALMSPID=Org1MSP" -e "CORE_PEER_MSPCONFIGPATH=/var/hyperledger/users/Admin@org1.example.com/msp" "$ORG1_PEER_NAME" peer channel create -o "$ORG1_ORDERER_NAME":7050 -c "$ORG1_CHANNEL_NAME" -f "$ORG1_CHANNEL_TX_LOCATION" 
```

Enter fullscreen mode Exit fullscreen mode

*   现在从一个独立的组织加入渠道，`fetch`是必要的。注意这里的`0`意味着我们正在获取第 0 个块。

```
 # fetch the channel block
    docker exec -e "CORE_PEER_LOCALMSPID=Org1MSP" -e "CORE_PEER_MSPCONFIGPATH=/var/hyperledger/users/Admin@org1.example.com/msp" "$PEER_NAME" peer channel fetch 0 -o "$ORDERER_NAME":7050 -c "$CHANNEL_NAME"

    # join the channel
    docker exec -e "CORE_PEER_LOCALMSPID=CHMSP" -e "CORE_PEER_MSPCONFIGPATH=/var/hyperledger/users/Admin@ch.example.com/msp" "$CH_PEER_NAME" peer channel join -b "$CHANNEL_NAME"_0.block 
```

Enter fullscreen mode Exit fullscreen mode

*   链码的创建是相似的，但是有趣的是，你必须`instantiate`在信道中的一个对等体上创建链码。对于其他对等体，您必须手动`install`链代码，但是不需要实例化。一个`invoke`命令应该获取最近实例化的链码，并且同时执行事务。

## 上弦

因此，如果您能够成功地调用和查询，您应该可以开始了。

很好地记录您的配置，创建一些节省时间的脚本，比如 ssh-ing 到服务中并从管理器节点本身执行上述命令。

将您的客户与上述服务联系起来，享受您的生产级结构网络设置。尝试将您的网络部署到 IBM cloud 或 AWS。

[![](img/7064af7a22011f76dbb81abbe5919701.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bjVd0BW1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://m.memegen.com/hx9szq.jpg)

如果您有任何疑问或建议，请在下面评论。