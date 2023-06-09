# CouchDB Replicator 数据库——简要概述

> 原文：<https://dev.to/codehakase/the-couchdb-replicator-database---a-short-overview-3j86>

**TL；DR:** 在本文中，我将概述 CouchDB 中的 replicator 数据库，以及如何在 CouchDB 中剥离复制任务

* * *

CouchDB 是一个完全拥抱网络的数据库。CouchDB 将您的数据存储为 JSON 文档，并允许您从 web 界面或其 [REST API](http://docs.couchdb.org/en/2.1.1/api/basics.html#api-basics) 轻松访问这些文档。我们不会深入讨论 couchdb，因为这超出了本文的范围——我很快会写一篇。

## 在 CouchDB 中复制

复制涉及一个源数据库和一个目标数据库，它们可以共存于同一个或不同的 CouchDB 实例上。复制的唯一目的和目标是在过程结束时，源数据库上的所有活动文档也在目标数据库中，并且在源数据库中删除的所有文档也在目标数据库中删除。

### 复制器数据库

这个数据库可以存在于任何 CouchDB 节点上。复制器数据库是您`PUT`或`POST`记录触发复制的地方，您发出`DELETE`来取消任何正在进行的复制集。复制器数据库的默认标签是*_ 复制器*。这个名称可以在 CouchDB 配置中随时更改。

> 在继续复制数据库之前，请注意，出于安全原因，CouchDB 默认配置为只监听 localhost/127.0.0.1。这意味着它不能复制远程 CouchDB 服务器。为了允许远程复制，CouchDB 必须绑定到 *0.0.0.0* ，这是在源服务器上设置的。要更改绑定地址，您可以从[蒲团配置页面](http://localhost:5984/_utils/config.html)或者在 CouchDB 配置中找到`bind_address`来完成。

### 复制器数据库文档的结构

要触发复制，您需要在 **_replicator** 数据库中创建一个文档。它的结构应该是这样的:

```
{  "_id":  "test_replication",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb",  "create_target":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

保存上面的文档，couchdb 守护进程用文档的内容触发一个复制。日志条目应该如下所示:

```
[Thu, 18 Apr 2018 19:43:59 WAT] [info] Document `test_replication` triggered replication `REV_+create_target`
[Thu, 18 Apr 2018 19:44:37 WAT] [info] Replication `REV_+create_target` finished (triggered by document `test_replication`) 
```

Enter fullscreen mode Exit fullscreen mode

当一个复制被触发时，文档被 CouchDB 更新一些新的字段:

```
{  "_id":  "test_replication",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb",  "create_target":  true,  "_replication_id":  "some-id-goes-here",  "_replication_state":  "triggered",  "_replication_state_time":  "2018-04-18T19:46:32"  } 
```

Enter fullscreen mode Exit fullscreen mode

关于 CouchDB 添加的新字段的小说明:

*   **_replication_id** -这是分配给复制操作的 id
*   **_ 复制状态**—复制的当前状态
*   **_ replication _ state _ time**-告知当前复制状态设置时间的时间戳

### 复制重复的单据？

可能会有两个或更多的文档被添加到 **_replicator** 数据库，定义相同的复制操作:

```
//  first  document  {  "_id":  "replication_1",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb"  }  //  second  document  {  "_id":  "replication_2",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb"  } 
```

Enter fullscreen mode Exit fullscreen mode

从上面我们可以看出，两个文档定义了相同的复制，唯一不同的是文档 id。CouchDB 肯定会触发这种复制，但这一次会发生一些别的事情。第一个文档`replication_1`，可能触发复制，CouchDB 用字段`_replicaton_id, _replication_state, and _replication_state_time`更新文档。然而，第二个文档`replication_2`并没有更新相同的字段，相反，CouchDB 只更新了一个字段——`_replication_id`，它与第一个文档中的设置相同。

```
//  after  replication  is  triggered  //  first  document  {  "_id":  "replication_1",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb",  "_replication_id":  "my-sample-replication-id",  "_replication_state":  "triggered",  "_replication_state_time":  "2018-04-18T19:46:32"  }  //  second  document  {  "_id":  "replication_2",  "source":  "mydb",  "target":  "http://someserver.com:5984/mydb",  "_replication_id":  "my-sample-replication-id"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 取消复制

要取消正在进行的复制，只需删除触发复制的文档。

### 典型的复制程序

在复制过程中，CouchDB 会比较源数据库和目标数据库，以确定它们之间的不同文档。CouchDB 是如何确定这些的？这是通过跟踪源数据库上的[变更提要](http://docs.couchdb.org/en/2.1.1/api/database/changes.html#changes)，并将文档与目标数据库进行比较来实现的。更改被成批提交到目的地，在那里它们会引入冲突。如果文件已经存在于目的地，并且具有相同的修订，则不会被传输。

复制任务将在到达更改馈送的末尾时完成。如果其 continuous 属性设置为 true，它将等待新的更改出现，直到任务被取消。复制任务还在目标上创建检查点文档，以确保重新启动的任务可以从其停止的地方继续，例如在它崩溃之后。

### 参考文献

[couch db 复制简介-指南](http://docs.couchdb.org/en/2.1.1/replication/intro.html?highlight=Replication)

## 结论

万岁！我们刚刚总结了 CouchDB replicator 数据库的概述，以及一些复制术语。

我倾向于在这个话题上写更多，因为它是一个广泛的话题。

我错过了什么重要的事情吗？让我在下面的评论中知道:D

> 最初发布在[我的博客](https://hakaselogs.me/2018-04-19/couchdb-replicator-database)