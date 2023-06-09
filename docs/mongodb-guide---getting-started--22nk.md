# MongoDB 指南-入门

> 原文：<https://dev.to/drminnaar/mongodb-guide---getting-started--22nk>

## 总结

在本指南中，我将介绍使用 MongoDB 的基本知识。开始学习新技术的最好方法是直接投入并开始使用它。因此，在简单介绍了 MongoDB 之后，接下来的部分都是关于使用 4 种不同的托管方法来设置自己的 MongoDB 实例。接下来，我将介绍 5 个工具，您可以使用它们来处理 MongoDB 数据库。下一节说明了创建自己的数据库和数据集合的方法。在这一节中，我还提供了一些例子来说明查询数据的一些基本查询。

**本指南总结如下:**

*   什么是 MongoDB
*   MongoDB 设置
    *   本地安装和托管
    *   使用 Docker 安装和托管
    *   使用 MongoDB Atlas 的主机
    *   带有 MLab 的主机
*   MongoDB 工具
    *   MongoDB Shell
    *   MongoDb 罗盘
    *   NoSqlBooster
    *   Robo 3T
    *   Visual Studio 代码
*   基本 MongoDB 命令
*   结论

最后，本指南并没有涵盖使用 MongoDB 的原因(可能是另一个时间的指南)。但是，我鼓励读者花一些时间研究和理解选择 NoSql 数据库还是 Sql 数据库的基本原理。MongoDB 不是 SQL 数据库的替代品。这仅仅是一种选择，可能(也可能不)更好地符合个人的特定需求。

Quora 上发布的以下问题可能是一个很好的起点:

在 NoSQL 上使用传统 RDBMS 的一些原因是什么？

从我个人的经验和角度来看，我会提到，如果您的数据需要是不可知的，那么在使用 NoSql 数据库之前应该非常仔细地考虑。在这种情况下，我发现 Sql 数据库是一个更好的选择，因为它简化了数据建模，并为许多不同的应用程序以许多不同的方式公开数据。

原版指南可以在我的 [Github 库](https://github.com/drminnaar/guides/tree/master/mongodb-guide)上找到。像我的 [Docker 指南](https://github.com/drminnaar/guides/tree/master/docker-guide)一样，随着时间的推移，我将更新和添加更多的内容到这个库中。

* * *

## 什么是 MongoDB？

MongoDB 是一个数据库。更具体地说，它是一个面向文档的开源数据库，为开发人员和系统管理员设计了可伸缩性和简单性。传统的关系数据库管理系统(RDBMS)如 MSSQL、Oracle、MySQL 和 PostGreSQL 将数据存储在具有由行和列组成的静态模式的表中。然而，MongoDB 将其数据存储在具有动态模式的类似 JSON 的文档中。

* * *

## MongoDB 设置

我将讨论启动和运行 MongoDB 的 4 个选项:

*   本地安装和托管
*   在 Docker 中安装和托管
*   注册并使用 MongoDB Atlas(数据库即服务)
*   注册并使用 MLab(数据库即服务)

### 本地安装和托管

安装 MongoDB 相对简单。目前有 3 个平台(Windows、Linux、OSX)发布，可以在[这里](https://www.mongodb.com/download-center?jmp=homepage#community)找到

有关更具体的安装说明，请参见以下链接:

*   [在 Linux 上安装 MongoDB](https://docs.mongodb.com/v3.0/administration/install-on-linux)
*   [在 Windows 上安装 MongoDB](https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-windows)
*   [在 OSX 安装 MongoDB](https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-os-x)

### 使用 Docker 安装和托管

如果你以前从未使用过 [Docker](https://www.docker.com) ，那么这个选项不太适合你，因为它意味着你需要一些 Docker 知识和经验。然而，如果你仍然有兴趣追求这一选择，那么我建议查看我的[码头指南]((https://github.com/drminnaar/guides/tree/master/docker-guide))作为起点。我还有一张[码头工人备忘单](https://github.com/drminnaar/cheatsheets/blob/master/docker-cheatsheet.md)，你可能会觉得有用。

将要使用的 Docker 映像是官方 Docker MongoDB 映像。

#### 运行 MongoDB

##### 使用命名卷运行 MongoDB

对于下面的例子，我将 Docker MongoDB 端口 ***27017*** 映射到本地端口 ***37017*** 。这是因为我在 MongoDB 上运行的一个本地实例已经使用了 ***27017*** 端口。

要运行一个新的 MongoDB 容器，从 CLI 执行以下命令:

```
docker run --rm --name mongo-dev -p 37017:27017 -v mongo-dev-db:/data/db -d mongo 
```

Enter fullscreen mode Exit fullscreen mode

| CLI 命令 | 描述 |
| --- | --- |
| - rm | 停止时移除容器 |
| -名字叫蒙戈·德夫 | 给容器一个自定义名称 |
| -p | 将容器发布端口映射到本地端口 |
| -v mongo-dev-db/data/db | 将容器卷“data/db”映射到自定义名称“mongo-dev-db” |
| -迪·蒙戈 | 在后台将 mongo 容器作为守护进程运行 |

##### 使用绑定挂载运行 MongoDB

```
cd
mkdir -p mongodb/data/db
docker run --rm --name mongo-dev -p 37017:27017 -v ~/mongodb/data/db:/data/db -d mongo 
```

Enter fullscreen mode Exit fullscreen mode

| CLI 命令 | 描述 |
| --- | --- |
| - rm | 停止时移除容器 |
| -名字叫蒙戈·德夫 | 给容器一个自定义名称 |
| -p | 将容器发布端口映射到本地端口 |
| -v ~/mongodb/data/db/data/db | 将容器卷“data/db”映射到绑定装载“~/mongodb/data/db” |
| -迪·蒙戈 | 在后台将 mongo 容器作为守护进程运行 |

#### 访问蒙古数据库

##### 从本地机器访问 MongoDB

1.  键入以下命令来访问托管在 docker 容器中的 MongoDB 实例:

```
 mongo --host localhost --port 37017 
```

Enter fullscreen mode Exit fullscreen mode

##### 通过 Docker 互动 TTY 访问 MongoDB

访问 MongoDB shell 有两个步骤。

1.  首先，通过执行以下命令来访问 MongoDB 容器外壳:

```
 docker exec -it mongo-dev bash 
```

Enter fullscreen mode Exit fullscreen mode

这将在 MongoDB 容器上打开一个交互式 shell (bash)。

1.  其次，一旦进入容器外壳，通过执行以下命令访问 MongoDB 外壳:

```
 mongo localhost 
```

Enter fullscreen mode Exit fullscreen mode

### 主机用 MongoDB 图册

[MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 是一个*数据即服务(DaaS)* 产品，托管在云中。不需要安装 MongoDB，有一个免费层可用。

首先，在此注册一个免费等级帐户[，即可免费注册。免费层赋予您 512MB 的存储空间。](https://www.mongodb.com/cloud/atlas)

请查看[MongoDB Atlas 文档]以了解更多信息。

一旦您在 *Atlas* 上注册并设置了您的 MongoDB 实例，您将看到一个类似下图的仪表板:

[![atlas](img/dbf4ea41e52258c45f050c0701f1398a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--43pCJ1X7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/33935506/37251882-d19a3818-2520-11e8-97f6-7015435f3cbd.png)

### 主机带 MLab

[MLab](https://mlab.com) 是一个*数据即服务(DaaS)* 产品，托管在云中。不需要安装 MongoDB，有一个免费层可用。

要开始使用，请在此注册免费帐户[。免费层赋予您 500MB 的存储空间。](https://mlab.com/signup/)

请查阅 [MLab 文档](https://docs.mlab.com)了解更多信息。

一旦您在 *MLab* 上注册并设置了您的 MongoDB 实例，您将看到一个类似下图的仪表板:

[![mlab](img/860b786abc612cb53f16feb21fbabd5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mZN3bWrJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/33935506/37251951-ebb4dd74-2521-11e8-88bc-7db18f0a208c.png)

* * *

## MongoDB 工具

MongoDB 的工具最近有了很大的改进，并且有了很多选择。我使用过以下工具，我发现它们都很好。这确实是个人喜好的问题，但我发现我倾向于一起使用它们。

### MongoDB shell

MongoDB shell 是与 MongoDB 数据库交互的默认方式。

*   连接到 MongoDB

```
 mongo localhost 
```

Enter fullscreen mode Exit fullscreen mode

*   列出可用的数据库

```
 show dbs 
```

Enter fullscreen mode Exit fullscreen mode

*   创建数据库

```
 use message_db 
```

Enter fullscreen mode Exit fullscreen mode

*   创建收藏

```
 db.messages.insert({ message: 'hello mongodb' }) 
```

Enter fullscreen mode Exit fullscreen mode

*   列出收藏

```
 show collections 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】MongoDB 罗盘](#mongodb-compass)

MongoDB Compass 是一个图形用户界面(GUI)工具，允许用户探索您的 MongoDB 数据。

*   它有一个免费的附件
*   它是跨平台的，可用于 Linux、Windows 和 Mac
*   它支持以下主要功能:

    *   运行即席查询
    *   对数据执行 CRUD 操作
    *   查看和优化查询性能

在下面的截图中，我使用 MongoDB Compass 连接到运行在本地机器上的 MongoDB。

[![compass](img/5463fcc21107e6e4d46d911ca3380245.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ojAninK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/33935506/37252219-1e877398-2526-11e8-998a-6ecd6bcacaed.png)

关于 MongoDB Compass 的更多信息，请点击[这里](https://www.mongodb.com/products/compass)

### NoSqlBooster

NoSqlBooster 是一个图形用户界面(GUI ),它提供了一个易于使用的界面来处理 MongoDB 数据库。

*   它有一个免费的附件
*   它是跨平台的，可用于 Linux、Windows 和 Mac
*   它提供了以下功能:

    *   外壳扩展
    *   MongoDB 的流畅查询 API
    *   用 SQL 查询 MongoDB
    *   在脚本中使用节点模块

在下面的截图中，我使用 NoSqlBooster 连接到运行在 Docker 上的 MongoDB。

[![nosqlbooster](img/06d26d3fc34e4e7bbb30dc93d277bb0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KGpoE5nP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/33935506/37252401-18e5b4a6-2529-11e8-940f-9ad49aa82a4a.png)

更多信息请访问[官方 NoSqlBooster 网站](https://nosqlbooster.com/)。

### Robo 3T

Robo 3T 是一个免费的 GUI 工具，可以用来探索你的 MongoDB 数据库。

*   它是免费使用的。
*   它是跨平台的，可用于 Linux、Windows 和 Mac
*   它提供了一个内嵌 shell 的 MongoDB GUI

在下面的截图中，我使用 Robo 3T 连接到 MongoDB 的一个本地实例:

[![robo3t](img/fb0e6566eddd8c32a5fc5458279fe4aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WbAE8WYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/33935506/37252488-3fe78308-252a-11e8-94a7-36c528dca556.png)

[Robo 3T](https://robomongo.org) 可以下载[这里](https://robomongo.org/download)

欲了解更多信息，请点击[此处](https://robomongo.org/)。

### Visual Studio 代码

使用 [Azure CosmosDB 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)，除了 [Azure CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db) 数据库之外，还可以连接到 MongoDB 数据库。

有关更多信息，请参见以下链接:

*   [Azure CosmosDB 扩展上市](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)
*   Github 上的 Azure CosmosDB 扩展

#### 安装 Azure CosmosDB 扩展

*   启动 VS 代码
*   从 VS 代码中启动快速打开(`ctrl+P`)
*   粘贴以下命令并按 enter 键

```
 ext install ms-vscode.vscode-azureextensionpack 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置用户设置

一旦安装了扩展，您需要确保在您的用户设置中配置了一个到 Mongo 的路径。按`Ctrl+,`打开您的用户设置。因此，因为我使用的是基于 Linux 的操作系统，所以我必须在用户设置 json 中添加以下设置:

```
"mongo.shell.path":  "/var/lib/mongodb", 
```

Enter fullscreen mode Exit fullscreen mode

#### 连接本地主机上的 Mongo

要连接到 MongoDB 实例，请遵循以下步骤:

*   展开浏览器中的 Azure Cosmos DB 扩展面板
*   选择“附加数据库帐户”选项
*   选择 MongoDB
*   输入 MongoDB 实例的连接详细信息

```
 mongodb://localhost:27017 
```

Enter fullscreen mode Exit fullscreen mode

这些步骤如下所示:

[![vs-code-cosmodb-ext](img/03df5debf2c0420978b48bc6ab5e6e96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i1DJ8juQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/33935506/37252725-eafbe816-252e-11e8-93ef-155a1e9816bc.gif)

#### 使用 Mongo 剪贴簿运行命令

要使用*“剪贴簿功能”*，请遵循以下步骤:

*   如上所示，附加到 MongoDB 实例
*   连接到您选择的数据库
*   选择“新 MongoDB 剪贴簿”选项打开剪贴簿编辑器
*   输入 MongoDB 命令
*   按`Ctrl+Shft+'`执行

这些步骤如下所示:

[![vs-code-scrapbook](img/afd88abce4563b5b310d160d3ef92d02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TIjdIhWK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/33935506/37253010-8797dee6-2534-11e8-9014-357f6b39c881.gif)

在撰写本文的时候，似乎有一个关于 Mongo 剪贴簿的问题

* * *

## 基本的 MongoDB 命令

一旦运行或托管了 MongoDB 实例，打开 MongoDB 服务器的 mongo shell。出于本演示的目的，我将使用我的本地安装“localhost”。

*   连接到 MongoDB 实例

```
 mongo localhost 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个“zips_db”数据库

```
 use zips_db 
```

Enter fullscreen mode Exit fullscreen mode

*   创建“拉链”系列

```
 db.zips.insertMany([
     { "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 5338, "state" : "MA", "_id" : "01001" },
     { "city" : "CUSHMAN", "loc" : [ -72.51564999999999, 42.377017 ], "pop" : 36963, "state" : "MA", "_id" : "01002" },
     { "city" : "BARRE", "loc" : [ -72.10835400000001, 42.409698 ], "pop" : 4546, "state" : "MA", "_id" : "01005" },
     { "city" : "BELCHERTOWN", "loc" : [ -72.41095300000001, 42.275103 ], "pop" : 10579, "state" : "MA", "_id" : "01007" },
     { "city" : "BLANDFORD", "loc" : [ -72.936114, 42.182949 ], "pop" : 240, "state" : "MA", "_id" : "01008" },
     { "city" : "BRIMFIELD", "loc" : [ -72.188455, 42.116543 ], "pop" : 706, "state" : "MA", "_id" : "01010" },
     { "city" : "CHESTER", "loc" : [ -72.988761, 42.279421 ], "pop" : 688, "state" : "MA", "_id" : "01011" },
     { "city" : "CHESTERFIELD", "loc" : [ -72.833309, 42.38167 ], "pop"  177, "state" : "MA", "_id" : "01012" },
     { "city" : "CHICOPEE", "loc" : [ -72.607962, 42.162046 ], "pop" : 3396, "state" : "MA", "_id" : "01013" },
     { "city" : "CHICOPEE", "loc" : [ -72.576142, 42.176443 ], "pop" : 1495, "state" : "MA", "_id" : "01020" }
   ]) 
```

Enter fullscreen mode Exit fullscreen mode

*   搜索查询

    *   获取总收集计数

```
 db.zips.count() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取所有 zip 文档

```
 db.zips.find().pretty() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取 5 个 zip 文档

```
 db.zips.find().limit(5).pretty() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取城市名为“布兰德福德”的邮政编码

```
 db.zips.find({"city": "BLANDFORD"}) 
```

Enter fullscreen mode Exit fullscreen mode

*   获取城市名为“布兰德福德”的邮政编码，忽略大小写

```
 db.zips.find({"city": { "$regex": /blandford/i } }) 
```

Enter fullscreen mode Exit fullscreen mode

*   获取城市名为“布兰德福德”和“布里菲尔德”的邮政编码

```
 db.zips.find({"city": { "$in": ["BLANDFORD", "BRIMFIELD"] } }).pretty() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取城市名为“布兰德福德”和“布里菲尔德”的邮政编码，忽略大小写

```
 db.zips.find({"city": { "$in": [ /blandford/i, /brimfield/i] } }).pretty () 
```

Enter fullscreen mode Exit fullscreen mode

*   获取人口大于或等于 30000 的 zip 文档

```
 db.zips.find({"pop": { "$gte": 30000}}).pretty() 
```

Enter fullscreen mode Exit fullscreen mode

*   获取所有 zip 文档，并使用投影仅显示城市和人口

```
 db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 }) 
```

Enter fullscreen mode Exit fullscreen mode

*   获取所有 zip 文档，并使用投影仅显示城市和人口。按城市名称降序排列文档

```
 db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 }).sort({"city": -1}) 
```

Enter fullscreen mode Exit fullscreen mode

*   删除操作

    *   删除所有名为“奇科皮”的城市。忽略大小写

```
 db.zips.remove({ "city": { "$regex": /chicopee/i }}) 
```

Enter fullscreen mode Exit fullscreen mode

*   更新操作

    *   更新布兰德福德市，将其人口设置为 10

```
 db.zips.updateOne({"city": "BLANDFORD"}, { "$set": { "pop": 10}}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

这是对 MongoDB 入门的快速介绍。在本指南中，我们学习了托管 MongoDB 实例的 4 种不同方式。我们还了解了可以用来管理 MongoDB 数据库的 5 种不同的工具。最后，我们探讨了一些基本查询，这些查询可用于创建数据库、创建集合、插入数据、更新数据和获取数据。

MongoDB 是一个令人兴奋的相对较新的数据库，它有一个庞大的社区和知识库。我鼓励所有感兴趣的人参与进来，尝试以下学习资源:

*   官方的 MongoDB 文档
*   MongoDB 大学-MongoDB 的免费在线课程

下面这本 MongoDB 是我个人最喜欢的:

*   MongoDB:权威指南，第二版 -我的个人
*   [MongoDB:权威指南，第三版(早期发布)](http://shop.oreilly.com/product/0636920049531.do)

如果你对认证感兴趣，请查看 [MongoDB Professional
认证项目](https://university.mongodb.com/certification)。

* * *