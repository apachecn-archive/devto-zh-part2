# 利用 MongoDB Atlas 中的跨区域复制优化快速响应读取

> 原文：<https://dev.to/mongodb/optimizing-for-fast-responsive-reads-with-cross-region-replication-in-mongodb-atlas-28d7>

MongoDB Atlas 客户可以实现跨区域复制，以实现多区域容错和快速响应的读取。

*   通过将副本集成员分布在多个区域，可以提高可用性保证。如果主节点(或包含主节点的云区域)离线，这些辅助节点将参与自动选举和故障转移过程。
*   只读副本集成员允许客户使用单个 MongoDB 部署跨不同地理区域优化本地读取(最小化读取延迟)。这些复制集成员将不参与选举和故障转移过程，并且永远不能被选为主复制集成员。

在本文中，我们将更深入地探讨使用跨区域复制优化本地读取，并带您在 AWS 上运行的环境中完成必要的配置步骤。

### 引子上的阅读偏好

[Read preference](https://docs.mongodb.com/manual/core/read-preference/) 决定 MongoDB 客户机如何将读操作路由到副本集的成员。默认情况下，应用程序将其读取操作指向副本集[主副本集](https://docs.mongodb.com/manual/reference/glossary/#term-primary)。通过指定读取首选项，用户可以:

*   为地理上分散的用户启用本地读取。例如，来自加利福尼亚的用户可以从位于本地的副本中读取数据，以获得响应速度更快的体验
*   在故障转移情况下，允许对数据库进行只读访问

[![](../Images/f3906d8fc4ab29b73e8db0e692ab3702.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U-UUADr1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image5-vh6i7z8j0q.png)

读取副本只是提供来自操作日志的复制数据的数据库的实例；客户端不会写入读取副本。

使用 MongoDB Atlas，我们可以轻松地将读取副本分布到多个云区域，只需点击几下鼠标，就可以将应用程序的数据扩展到包含主副本集的区域之外。

为了支持本地读取并增加应用程序的读取吞吐量，我们只需通过 MongoDB 驱动程序修改读取偏好。

### 在 MongoDB Atlas 中启用读取副本

我们可以在 Atlas UI 中为新的或现有的 MongoDB 付费集群启用读取副本。首先，我们可以单击集群“配置”按钮，然后找到名为 _ **“启用跨区域配置选项”的链接** _

当我们单击它时，会出现一个选项来选择我们想要的交叉复制类型。我们将选择 _ **部署只读副本** _:

[![](../Images/c2fe13838b41603d305e31044dffcd40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--McPaBlD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image1-q7z5p2iagp.gif)

正如您在上面看到的，我们将首选区域(包含主副本集的区域)设置为 AWS，us-east-1(弗吉尼亚),默认有三个节点。我们可以根据我们认为应用程序的其他用户可能集中的位置，向集群配置中添加区域。在这种情况下，我们将在 us-west-1(北加利福尼亚)和 eu-west-1(爱尔兰)中添加额外的节点，为我们提供读取副本来服务本地用户。

请注意，所有写入操作仍将在我们的首选区域中进行，从我们添加的区域中的辅助节点进行的读取操作将最终保持一致。

[![](../Images/3835d3e874526389bddfd3f8c35c8ea7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--STsm451t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image4-l82awkjmnd.gif)

我们将单击“确认并部署”，这将部署我们的多区域集群。

[![](../Images/fd74757d68717f1c1208a1a1b75d00f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eNntX3OR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image3-tkhye8w0g6.png)

我们的默认连接字符串现在将包括这些读取副本。我们可以转到“Connect”按钮，找到完整的连接字符串来访问我们的集群:

[![](../Images/47e504c72b6bb963b2626e9fcf6dd7d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k9_4McTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image2-fpoel33crq.png)

当集群部署完成时，我们将准备好使用 MongoDB 驱动程序将应用程序的数据读取分布到多个区域。我们可以在连接字符串中专门配置 readPreference，以便将客户端发送到“最近的副本”。例如，[节点本地 MongoDB 驱动程序](https://mongodb.github.io/node-mongodb-native/index.html)允许我们指定我们的偏好:

| **readPreference** | 指定此连接的[副本集](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)读取首选项。 |
| --- | --- |
|  |  |

|读取首选项值如下:

*   [初级](https://docs.mongodb.com/manual/reference/read-preference/#primary)
*   [主要首选](https://docs.mongodb.com/manual/reference/read-preference/#primaryPreferred)
*   [二级](https://docs.mongodb.com/manual/reference/read-preference/#secondary)
*   [次级优先](https://docs.mongodb.com/manual/reference/read-preference/#secondaryPreferred)
*   [最近的](https://docs.mongodb.com/manual/reference/read-preference/#nearest)

对于我们的应用程序，如果我们想要确保我们的连接字符串中的[读取首选项被设置为最近的 MongoDB 副本，我们将如下配置它:](https://docs.mongodb.com/manual/reference/connection-string/) 

```
mongodb://admin:@cluster0-shard-00-00-bywqq.mongodb.net:27017,cluster0-shard-00-01-bywqq.mongodb.net:27017,cluster0-shard-00-02-bywqq.mongodb.net:27017,cluster0-shard-00-03-bywqq.mongodb.net:27017,cluster0-shard-00-04-bywqq.mongodb.net:27017/test?ssl=true&replicaSet=Cluster0-shard-0&authSource=admin?readPreference=nearest 
```

Enter fullscreen mode Exit fullscreen mode

### 安全性和连接性(AWS 上)

MongoDB Atlas 允许我们将应用服务器的 VPC 直接对等到同一地区的 MongoDB Atlas VPC。这允许我们减少网络对互联网的暴露，并允许我们使用本机 AWS 安全组或 CIDR 块。你可以在这里查看如何配置 [VPC 对等](https://docs.atlas.mongodb.com/security-vpc-peering/)。

***跨区域节点的 VPCs 注意:***

目前，MongoDB Atlas **不支持跨地区的 VPC 对等。如果您想授予一个云区域中的客户端对另一个云区域中的数据库实例的读或写访问权限，您需要允许客户端的公共 IP 地址通过 [IP 白名单](https://docs.atlas.mongodb.com/security-whitelist/)访问您的数据库部署。**

随着跨区域复制和只读副本的启用，您的应用程序现在将能够从任意数量的区域提供快速、响应性的数据存取。

* * *

今天就开始使用 MongoDB Atlas 管理的免费 512 MB 数据库[这里](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=cross-reg&jmp=dev-ref)。