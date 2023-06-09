# 您的第一个 Hyperledger 结构网络

> 原文：<https://dev.to/damcosset/your-first-hyperledger-fabric-network-2n67>

## 简介

在本文中，我们将使用 Hyperledger Fabric 构建一个简单的区块链网络。布料让我们可以建造一个私人的区块链。不涉及加密货币。在这之后，你会对启动你自己的私人区块链所需的不同部分有一个相当好的理解。在本例中，我们的网络将由 3 个组织组成。每个组织将有两个同行。

## 先决条件

*   你必须安装 **docker** 和 **docker-compose** 。参见 [Docker 安装页面](https://docs.docker.com/install/)。为了确保你已经安装了 Docker，你可以在你的终端中运行`docker -v`。这将返回如下内容:

```
Docker version 18.03.1-ce, build 9ee9f40 
```

Enter fullscreen mode Exit fullscreen mode

*   Fabric 已经给了我们一些网络样本来玩和探索。要获得它们，打开终端窗口并运行:`git clone -b master https://github.com/hyperledger/fabric-samples.git`

这将创建一个`fabric-samples`文件夹，里面有一些布料样品。这些文件夹里有很多有用的信息，但我们不会在这里探究它们。

*   接下来，我们将下载特定于平台的二进制文件。这将提取一些我们稍后需要的二进制文件。使用`cd fabric-samples`进入`fabric-samples`文件夹并运行:

```
curl -sSL https://goo.gl/6wtTN5 | bash -s 1.1.0 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的`fabric-samples`文件夹中创建一个`bin`文件夹。

就是这样！现在，我们准备从头开始构建我们的新网络！

## 隐原

首先，在你的`fabric-samples`文件夹中，创建一个名为`cool-network`的新文件夹，这是所有神奇事情发生的地方。然后，因为我们将使用 1.1.0 版本，所以让我们做一个`git checkout v1.1.0`，以确保不会因为版本而出现错误。

### 生成我们的证书和密钥

我们的第一步将是为组织及其内部的同行生成所有的证书和密钥。在我们的网络中，每个人都必须证明自己是谁。我们将生成的证书将证明他们的身份，并允许他们签署和验证交易。交易将由实体的私钥签名，并由公钥验证。

为了生成这些材料，我们将使用**密码**工具。我们通过下载特定于平台的二进制文件(在`bin`文件夹中有一个名为 cryptogen 的文件)获得了这个工具。 **cryptogen** 工具消耗一个`crypto-config.yaml`文件。所以，在我们的`cool-network`文件夹中，继续创建这个文件。你可以复制并粘贴下面的内容，我会解释这是怎么回事:

```
OrdererOrgs:
  - Name: OrderingService
    Domain: cool-network.com
    Specs:
      - Hostname: orderer

PeerOrgs:
  - Name: Microsoft
    Domain: microsoft.cool-network.com
    EnableNodeOUs: true
    Template:
      Count: 2
    Users: 
      Count: 1
  - Name: Apple
    Domain: apple.cool-network.com
    EnableNodeOUs: true
    Template:
      Count: 2
    Users: 
      Count: 1
  - Name: IBM
    Domain: ibm.cool-network.com
    EnableNodeOUs: true
    Template:
      Count: 2
    Users:
      Count: 1 
```

Enter fullscreen mode Exit fullscreen mode

该文件分为两部分:*订单订单*和*订单*。在我们研究这个文件之前，我们必须解释什么是*订购者*。

在结构中，订购服务是网络实体的集合。订购者确保交易按照正确的顺序进行(先到先得)。然后，订购者负责将交易打包成块。

那么，这份文件里有什么？我们首先定义订购者服务。我们在 specs 键下给它一个名称、一个域和一个主机名。命名约定如下:{Hostname}。{Domain}。因此，我们的订购者将被命名为*orderer.cool-network.com*。

接下来，我们定义我们的 3 个组织。我们定义他们的名字和领域。键 *EnableNodeOUs* 被设置为真。这超出了本文的范围，但是将它设置为 true 将允许我们为网络成员定义额外的策略。最后，我们将每个组织的计数键设置为 1。这意味着每个组织将有两个对等点。为什么？因为每个组织默认都有一个管理员。因此，组织中的同级人数将是[Count + 1]。

### 运行密码工具

现在，我们可以生成证书和密钥了。确保您在`cool-network`文件夹中，并运行:

`../bin/cryptogen generate --config crypto-config.yaml --output=crypto-config`

*cryptogen* 工具有一个生成方法，可以为我们完成这项工作。我们提供一个配置文件和一个输出文件夹。您应该会在您的终端中看到以下内容:

```
microsoft.cool-network.com
apple.cool-network.com
ibm.cool-network.com 
```

Enter fullscreen mode Exit fullscreen mode

而一个`crypto-config`文件刚刚出现在你的`cool-network`文件夹里！订购者、组织和对等方的所有证书和密钥都存储在这里。

## Configtxgen

接下来，我们将使用 configtxgen 工具。这个工具将做三件重要的事情:

*   创建有序的创世块
*   创建渠道配置交易
*   创建锚定对等交易，每个对等组织一个

**通道**:在 Fabric 中，您可以创建通道。这些通道允许组织在更大的网络内创建私有子网。在我们的例子中，我们可以想象一个只有微软和 IBM 参与的渠道，以及另一个只有苹果和 IBM 参与的渠道。

**锚节点**:信道上的锚节点是所有其他节点都可以发现并与之通信的公共节点。一个频道上的每个组织都有一个锚定对等点。

### 创建文件

configtxgen 工具使用一个 *configtx.yaml* 文件。创建它并复制粘贴以下:

```
# Profiles

Profiles:
    OrdererGenesis:
      Capabilities:
        <<: *ChannelCapabilities
      Orderer:
        <<: *OrdererDefaults
        Organizations:
          - *OrdererOrg
        Capabilities:
          <<: *OrdererCapabilities
      Consortiums:
        MyFirstConsortium:
          Organizations:
            - *Microsoft
            - *IBM
            - *Apple
    channelthreeorgs:
      Consortium: MyFirstConsortium
      Application:
        <<: *ApplicationDefaults
        Organizations:
          - *Microsoft
          - *IBM
          - *Apple
        Capabilities:
          <<: *ApplicationCapabilities

# Organizations

Organizations:

  - &OrdererOrg
    Name: OrderingService
    ID: OrdererMSP
    MSPDir: crypto-config/ordererOrganizations/cool-network.com/msp

  - &Microsoft
    Name: MicrosoftMSP
    ID: MicrosoftMSP
    MSPDir: crypto-config/peerOrganizations/microsoft.cool-network.com/msp
    AnchorPeers:
      - Host: peer0.microsoft.cool-network.com
        Port: 7051

  - &Apple
    Name: AppleMSP
    ID: AppleMSP
    MSPDir: crypto-config/peerOrganizations/apple.cool-network.com/msp
    AnchorPeers:
      - Host: peer0.apple.cool-network.com
        Port: 7051

  - &IBM
    Name: IBMMSP
    ID: IBMMSP
    MSPDir: crypto-config/peerOrganizations/ibm.cool-network.com/msp
    AnchorPeers:
      - Host: peer0.ibm.cool-network.com
        Port: 7051

# Orderer

Orderer: &OrdererDefaults

  OrdererType: solo
  Addresses: 
    - orderer.cool-network.com:7050
  BatchTimeout: 2s
  BatchSize:
    MaxMessageCount: 10
    AbsoluteMaxBytes: 99 MB
    PreferredMaxBytes: 512 KB
  Kafka:
    Brokers: 
      - 127.0.0.1:9092

  Organizations:

# Application

Application: &ApplicationDefaults

  Organizations:

# Capabilities

Capabilities:
    Global: &ChannelCapabilities
        V1_1: true
    Orderer: &OrdererCapabilities
        V1_1: true
    Application: &ApplicationCapabilities
        V1_1: true 
```

Enter fullscreen mode Exit fullscreen mode

这里有几个不同的部分。目前最重要的个人资料和组织。在简介中，我们描述了订购者 genesis block、我们的渠道(与三个组织)以及我们还定义了一个联盟。请注意标题(OrdererGenesis 和 channelthreeorgs ),因为稍后会用到它们。在组织中，我们指定每个组织的证书存储位置。我们刚刚用*密码*创建了这些证书。我们还为每个组织指定了锚定对等点。(MSP 指会员服务提供商)

*订购者*部分的*订购者类型*键设置为*独奏*。因为我们在一个开发环境中，我们只有一个订购者。如果我们在生产环境中，我们将定义几个订购者，以防其中一个或几个有问题。目前，这将做得很好。

### 运行 configtxgen

首先，创建一个名为`channel-artifacts`的文件夹来存放我们的工件。

我们现在可以运行 *configtxgen* 工具了。运行以下命令:

```
../bin/configtxgen -profile OrdererGenesis -outputBlock ./channel-artifacts/genesis.block 
```

Enter fullscreen mode Exit fullscreen mode

注意我们之前指定的 *OrdererGenesis* 头。这将为我们的订购者 genesis block 创建一个 *genesis.block* 文件。。您应该会在您的终端中看到以下内容:

```
2018-06-22 18:39:13.218 CEST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-06-22 18:39:13.251 CEST [msp] getMspConfig -> INFO 002 Loading NodeOUs
2018-06-22 18:39:13.252 CEST [msp] getMspConfig -> INFO 003 Loading NodeOUs
2018-06-22 18:39:13.254 CEST [msp] getMspConfig -> INFO 004 Loading NodeOUs
2018-06-22 18:39:13.254 CEST [common/tools/configtxgen] doOutputBlock -> INFO 005 Generating genesis block
2018-06-22 18:39:13.258 CEST [common/tools/configtxgen] doOutputBlock -> INFO 006 Writing genesis block 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们必须运行一个命令来创建我们的通道配置事务:

```
../bin/configtxgen -profile channelthreeorgs -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID channelthreeorgs 
```

Enter fullscreen mode Exit fullscreen mode

注意我们之前指定的*channel threorgs*头。这将创建一个 channel.tx 文件，它将出现在您的终端:

```
2018-06-22 18:41:42.241 CEST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-06-22 18:41:42.263 CEST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-06-22 18:41:42.267 CEST [msp] getMspConfig -> INFO 003 Loading NodeOUs
2018-06-22 18:41:42.271 CEST [msp] getMspConfig -> INFO 004 Loading NodeOUs
2018-06-22 18:41:42.273 CEST [msp] getMspConfig -> INFO 005 Loading NodeOUs
2018-06-22 18:41:42.317 CEST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 006 Writing new channel tx 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们必须创建锚节点事务。我们将运行三个命令，每个组织一个。对于微软:

```
../bin/configtxgen -profile channelthreeorgs -outputAnchorPeersUpdate ./channel-artifacts/MicrosoftAnchor.tx -channelID channelthreeorgs -asOrg MicrosoftMSP 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们使用了相同的通道头，并且使用了我们之前指定的 ID(Microsoft MSP)。我们指定一个输出文件(这里是 MicrosoftAnchor.tx)

这应该出现在你的终端:

```
2018-06-22 18:46:30.945 CEST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-06-22 18:46:30.973 CEST [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 002 Generating anchor peer update
2018-06-22 18:46:30.976 CEST [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 003 Writing anchor peer update 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在分别为 IBM 和 Apple 运行以下两个命令:

```
../bin/configtxgen -profile channelthreeorgs -outputAnchorPeersUpdate ./channel-artifacts/IBMAnchor.tx -channelID channelthreeorgs -asOrg IBMMSP

../bin/configtxgen -profile channelthreeorgs -outputAnchorPeersUpdate ./channel-artifacts/AppleAnchor.tx -channelID channelthreeorgs -asOrg AppleMSP 
```

Enter fullscreen mode Exit fullscreen mode

## 创建对等库和 docker-compose 文件

我们就快到了，我们现在需要指定一些 docker 文件来告诉我们的网络在哪里可以找到一些图像，如何进行配置...

首先，创建一个*基础*文件夹。在这个文件夹中，创建两个文件，一个名为 *docker-compose-base.yaml* ，另一个名为 peer-base.yaml

*peer-base.yaml*

```
version: '2'

services:
  peer-base:
    image: hyperledger/fabric-peer:x86_64-1.0.0-rc1
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=${COMPOSE_PROJECT_NAME}_byfn
      - CORE_LOGGING_LEVEL=INFO
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_GOSSIP_USELEADERELECTION=true
      - CORE_PEER_GOSSIP_ORGLEADER=false
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start 
```

Enter fullscreen mode Exit fullscreen mode

坞站-复合基座。yaml

```
version: '2'

services:

  orderer.cool-network.com:
    container_name: orderer.cool-network.com
    image: hyperledger/fabric-orderer:x86_64-1.0.0-rc1
    environment:
      - ORDERER_GENERAL_LOGLEVEL=INFO
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/genesis.block
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      # enabled TLS
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
    - ../channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
    - ../crypto-config/ordererOrganizations/cool-network.com/orderers/orderer.cool-network.com/msp:/var/hyperledger/orderer/msp
    - ../crypto-config/ordererOrganizations/cool-network.com/orderers/orderer.cool-network.com/tls/:/var/hyperledger/orderer/tls
    - orderer.cool-network.com:/var/hyperledger/production/orderer
    ports:
      - 7050:7050

  peer0.microsoft.cool-network.com:
    container_name: peer0.microsoft.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.microsoft.cool-network.com
      - CORE_PEER_ADDRESS=peer0.microsoft.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.microsoft.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.microsoft.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=MicrosoftMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/microsoft.cool-network.com/peers/peer0.microsoft.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/microsoft.cool-network.com/peers/peer0.microsoft.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer0.microsoft.cool-network.com:/var/hyperledger/production
    ports:
      - 7051:7051
      - 7053:7053

  peer1.microsoft.cool-network.com:
    container_name: peer1.microsoft.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.microsoft.cool-network.com
      - CORE_PEER_ADDRESS=peer1.microsoft.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.microsoft.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.microsoft.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=MicrosoftMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/microsoft.cool-network.com/peers/peer1.microsoft.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/microsoft.cool-network.com/peers/peer1.microsoft.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer1.microsoft.cool-network.com:/var/hyperledger/production

    ports:
      - 8051:7051
      - 8053:7053

  peer0.apple.cool-network.com:
    container_name: peer0.apple.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.apple.cool-network.com
      - CORE_PEER_ADDRESS=peer0.apple.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.apple.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.apple.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=AppleMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/apple.cool-network.com/peers/peer0.apple.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/apple.cool-network.com/peers/peer0.apple.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer0.apple.cool-network.com:/var/hyperledger/production
    ports:
      - 9051:7051
      - 9053:7053

  peer1.apple.cool-network.com:
    container_name: peer1.apple.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.apple.cool-network.com
      - CORE_PEER_ADDRESS=peer1.apple.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.apple.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.apple.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=AppleMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/apple.cool-network.com/peers/peer1.apple.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/apple.cool-network.com/peers/peer1.apple.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer1.apple.cool-network.com:/var/hyperledger/production
    ports:
      - 10051:7051
      - 10053:7053

  peer0.ibm.cool-network.com:
    container_name: peer0.ibm.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.ibm.cool-network.com
      - CORE_PEER_ADDRESS=peer0.ibm.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.ibm.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.ibm.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=IBMMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/ibm.cool-network.com/peers/peer0.ibm.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/ibm.cool-network.com/peers/peer0.ibm.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer0.ibm.cool-network.com:/var/hyperledger/production
    ports:
      - 11051:7051
      - 11053:7053

  peer1.ibm.cool-network.com:
    container_name: peer1.ibm.cool-network.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.ibm.cool-network.com
      - CORE_PEER_ADDRESS=peer1.ibm.cool-network.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.ibm.cool-network.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.ibm.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=IBMMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/ibm.cool-network.com/peers/peer1.ibm.cool-network.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/ibm.cool-network.com/peers/peer1.ibm.cool-network.com/tls:/etc/hyperledger/fabric/tls
        - peer1.ibm.cool-network.com:/var/hyperledger/production
    ports:
      - 12051:7051
      - 12053:7053 
```

Enter fullscreen mode Exit fullscreen mode

这些文件为我们的网络建立了基础，包括一些结构图像和一些环境变量。

最后，我们将添加的最后一个文件是一个 *docker-compose-cli.yaml* 文件。这个文件将位于我们的`cool-network`项目的根目录下。该文件创建了一个`cli`容器，并引导我们在`base`文件夹中声明的所有组织。

坞站-组合-cli.yaml

```
 version: '2'

volumes:
  orderer.cool-network.com:
  peer0.microsoft.cool-network.com:
  peer1.microsoft.cool-network.com:
  peer0.apple.cool-network.com:
  peer1.apple.cool-network.com:
  peer0.ibm.cool-network.com:
  peer1.ibm.cool-network.com:

networks:
  byfn:

services:

  orderer.cool-network.com:
    extends:
      file:   base/docker-compose-base.yaml
      service: orderer.cool-network.com
    container_name: orderer.cool-network.com
    networks:
      - byfn

  peer0.microsoft.cool-network.com:
    container_name: peer0.microsoft.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.microsoft.cool-network.com
    networks:
      - byfn

  peer1.microsoft.cool-network.com:
    container_name: peer1.microsoft.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.microsoft.cool-network.com
    networks:
      - byfn

  peer0.apple.cool-network.com:
    container_name: peer0.apple.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.apple.cool-network.com
    networks:
      - byfn

  peer1.apple.cool-network.com:
    container_name: peer1.apple.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.apple.cool-network.com
    networks:
      - byfn

  peer0.ibm.cool-network.com:
    container_name: peer0.ibm.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.ibm.cool-network.com
    networks:
      - byfn

  peer1.ibm.cool-network.com:
    container_name: peer1.ibm.cool-network.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.ibm.cool-network.com
    networks:
      - byfn

  cli:
    container_name: cli
    image: hyperledger/fabric-tools:x86_64-1.0.0-rc1
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      #- CORE_LOGGING_LEVEL=DEBUG
      - CORE_LOGGING_LEVEL=INFO
      - CORE_PEER_ID=cli
      - CORE_PEER_ADDRESS=peer0.microsoft.cool-network.com:7051
      - CORE_PEER_LOCALMSPID=MicrosoftMSP
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/microsoft.cool-network.com/peers/peer0.microsoft.cool-network.com/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/microsoft.cool-network.com/peers/peer0.microsoft.cool-network.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/apple.cool-network.com/peers/peer0.apple.cool-network.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/apple.cool-network.com/users/Admin@apple.cool-network.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run/
        - ./../chaincode/:/opt/gopath/src/github.com/chaincode
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    depends_on:
      - orderer.cool-network.com
      - peer0.microsoft.cool-network.com
      - peer1.microsoft.cool-network.com
      - peer0.apple.cool-network.com
      - peer1.apple.cool-network.com
      - peer0.ibm.cool-network.com
      - peer1.ibm.cool-network.com
    networks:
      - byfn 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在我们可以运行`docker-compose -f docker-compose-cli.yaml up -d`。您应该会看到这样的内容，这是您的终端:

```
WARNING: The COMPOSE_PROJECT_NAME variable is not set. Defaulting to a blank string.
Creating network "cool-network_byfn" with the default driver
Creating volume "cool-network_orderer.cool-network.com" with default driver
Creating volume "cool-network_peer0.microsoft.cool-network.com" with default driver
Creating volume "cool-network_peer1.microsoft.cool-network.com" with default driver
Creating volume "cool-network_peer0.apple.cool-network.com" with default driver
Creating volume "cool-network_peer1.apple.cool-network.com" with default driver
Creating volume "cool-network_peer0.ibm.cool-network.com" with default driver
Creating volume "cool-network_peer1.ibm.cool-network.com" with default driver
Creating peer1.apple.cool-network.com     ... done
Creating peer0.apple.cool-network.com     ... done
Creating orderer.cool-network.com         ... done
Creating peer1.microsoft.cool-network.com ... done
Creating peer1.ibm.cool-network.com       ... done
Creating peer0.ibm.cool-network.com       ... done
Creating peer0.microsoft.cool-network.com ... done
Creating cli                              ... done 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以用`docker start cli`运行我们的`cli`容器，并用`docker exec -it cli bash` :
进入容器内部

```
➜  cool-network git:(1252c7a) ✗ docker exec -it cli bash
root@9f8760ee6be8:/opt/gopath/src/github.com/hyperledger/fabric/peer# 
```

Enter fullscreen mode Exit fullscreen mode

Tadaaaa，我们的网络正在运行！！您可以浏览不同的文件夹，您会发现所有的证书、变量以及我们在 yaml 文件中定义的所有内容。

## 结论

我希望本教程对你理解如何设置 Hyperledger Fabric 环境足够清晰。当然，就目前而言，没有任何链码(或智能合约)，这个网络做不了多少事情。这将是后续文章的主题。玩得开心！