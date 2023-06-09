# MongoDB:数据库变得简单

> 原文：<https://dev.to/rmadisetti3/mongodb-databases-made-simple-3jh5>

[![alt text](img/9b1fe520890e21816e3e8264cf920fbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WZvRm2fk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qw4l1vcq9ohkgf5fppc.png)

各位 Javascript 和 Node.js 编码员好，

本文将向您介绍 Mongo 和 MongoDB 的基础知识及其有用的特性。

首先，MongoDB 被归类为 NoSQL 数据库，这意味着它以用户想要的任何结构存储 JSON (Javascript Object Notation)文档。为了说明其灵活性，数据库的每个元素可以拥有不同的字段，并且整个数据库的整体结构可以总是被修改。MongoDB 使用动态“模式”将每个文档组织成更大的集合，该模式单独映射每个文档。

由于在处理所有类型的数据方面相对容易，MongoDB 是一个广泛使用的数据库，它可以根据 Node.js 应用程序进行调整和改造，而 node . js 应用程序有时不符合标准的数据库方法，即在表中包含传统的行和列模型。MongoDB 还具有分片能力，这意味着数据库可以快速有效地在大型计算机/机器组之间分发数据。此外，MongoDB 是一个免费的开源数据库程序，吸引了许多预算有限的开发人员，该程序支持 C、C++、C#、Javascript、Node.js、Java、PHP、Python 和许多其他语言。

为了与大量数据库进行实际交互，程序员使用 mongo shell Javascript 终端与数据进行交互，并与 MongoDB 服务器的另一个运行实例进行连接。首先，必须运行带有命令`mongod`的终端窗口，并且必须使用另一个终端窗口来开发和编辑数据库。在另一个终端窗口中，必须运行命令`mongo`。然后，可以用`db [name of database]`，然后用`use [name of database]`，创建一个数据库。要退出，你可以写`quit()`或按`<CTRL-C>`。