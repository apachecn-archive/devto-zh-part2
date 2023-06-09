# Postgres 中的事件存储

> 原文：<https://dev.to/kspeakman/event-storage-in-postgres-4dk2>

任何基础架构的关键部分之一都是存储。与传统的关系模型相比，在日志中存储事件非常简单。然而，当您体验到一个成功产品的好运时，即使是日志风格的存储也必须发展才能跟上。

## 天真的实现

当我开始使用事件源时，我想尽可能简单。老实说，我无法理解常见事件源数据库实现的许多陷阱。所以我从一张简单的桌子开始。

```
CREATE TABLE Event
(
    SequenceNum bigserial NOT NULL,
    StreamId uuid NOT NULL,
    Data jsonb NOT NULL,
    Type text NOT NULL,
    Meta jsonb NOT NULL,
    LogDate timestamptz NOT NULL DEFAULT now(),
    PRIMARY KEY (SequenceNum)
);

CREATE INDEX idx_event_streamid ON Event (StreamId); 
```

Enter fullscreen mode Exit fullscreen mode

我用的是连载作家。(翻译:所有请求都被排队，一次处理一个，没有并发性。)我用了全一致性。(翻译:保存事件和更新关系/文档模型是在同一个数据库的同一个事务中执行的。)

这种配置对我们来说工作得很好，具有良好的性能(甚至在 t2.micro 实例上)，并且相对容易理解。如果这是一个内部系统，我可能会就此打住。但是对于一个多租户应用程序来说，在某个时候这将会遇到一个可扩展的砖墙。

> 💡**旁白-事件源岩石**
> 当我们改变我们的关系模型时，我们的“数据迁移”包括*删除*受影响的表，用更新的模式重新创建它们，并向它们重放事件。该过程在部署过程中自动发生。我们从来不需要编写迁移脚本。< 3

## 成长实现

事件存储的下一个版本包括一些优化，以支持进一步的扩展能力。其中大部分在 7 年前的[构建事件存储](https://cqrs.wordpress.com/documents/building-event-storage/)中有所描述。然而，当时我并没有完全理解描述中的一些细微差别。与其用不熟悉的武器搬起石头砸自己的脚，我选择先实现简单的版本。现在我开始着手优化。

### 事件表

让我们从事件表本身开始。尽管这个更新后的表几乎是相同的，但我将继续解释每个字段和索引的用途。

```
CREATE TABLE IF NOT EXISTS Event
(
    SequenceNum bigserial NOT NULL,
    StreamId uuid NOT NULL,
    Version int NOT NULL,
    Data jsonb NOT NULL,
    Type text NOT NULL,
    Meta jsonb NOT NULL,
    LogDate timestamptz NOT NULL DEFAULT now(),
    PRIMARY KEY (SequenceNum),
    UNIQUE (StreamId, Version),
    FOREIGN KEY (StreamId)
        REFERENCES Stream (StreamId)
); 
```

Enter fullscreen mode Exit fullscreen mode

#### 字段

仅有的两个绝对必填字段是`StreamId`和`Data`。其他一切都支持附加功能。

`SequenceNum`

This is used by event listeners to keep track of their current position in the stream. The only thing that really matters here is to preserve the order in which events were saved, which an auto-increment big integer does nicely.

`StreamId`

An identifier for a stream. I chose uuid but there are [many viable choices](https://softwareengineering.stackexchange.com/questions/301620/why-do-some-prominent-web-sites-use-alphanumeric-strings-for-resource-ids-instea/301641#301641) for identity.

`Version`

This field was not present in the previous incarnation, but was added as an optimization. If you think of the stream as an array, this is the index number of the event. Saving it with the event helps avoid manual counting.

`Data`

This is the serialized data for the event. I chose jsonb format, but there are other options. If I had more resources to develop my own introspection tools, I would probably use a binary serialization like Protocol Buffers for speed.

`Type`

This is the type of the event. Storing this supports filtering... to avoid fetch / deserialization if the listener does not use the event. I also use it for deserialization.

`Meta`

This supports auditing and tracing. I put things in here like user, execute permission, correlation id, etc.

`LogDate`

In addition to auditing and tracing, this could also be used for cross-shard sorting. Although it is tempting to use LogDate in business logic, it should only be used for infrastructural purposes. Events should contain their own timestamps if temporal context is needed. But I admit I have used LogDate for reports. :S

#### 索引/键

`SequenceNum` Primary Key

Allows efficient queries based on position. i.e. `WHERE SequenceNum > @LastSeenSeqNum`

`StreamId, Version` Unique Key

Allows efficient loading of a specific stream in Version order. Postgres can use this index to process `ORDER BY Version` without an extra sort step.

`StreamId` Foreign Key

This is primarily "training wheels" to ensure data integrity in the face of bugs. Once the code is battle-proven, this could be removed to increase performance.

> ⚠️ **来自未来的提示**
> 事实证明，在这里使用`bigserial`对于并发性来说是有问题的。由于 PG 序列的工作方式，有时会发生数字被打乱顺序的情况。例如，SequenceNum 7 的事件在 SequenceNum 6 之前写入。这可能会导致活动侦听器(通过 LISTEN/NOTIFY)错过事件。我最终不得不使用一个常规的`bigint`，并使用一个单独的单行表来跟踪已经发出的内容。更深入的探究请见评论。因为没有使用下表，所以`StreamId`外键也被删除。

### 流表

我们想要添加到事件存储中的一个特性是对并发写入器的支持。并发作者意味着:我可以部署我的业务服务的多个副本(那些生成事件的东西，也称为命令处理程序),而不需要它们之间的锁或协调。我们甚至可以在 Lambda 这样的“无服务器”架构上运行这些程序，按需自动扩展计算资源。这是通过使用开放式并发来实现的。你可以把这想象成 git 中的合并冲突——两个不同的分支独立地对同一行代码进行修改。在这种情况下，两个独立的用户试图同时对同一个实体进行更改。这是我们用来检测的表格。

> ⚠️ **来自未来的提示**
> 我对 Lambda 命令处理程序的分析是，它不是一个好主意。只是因为 Lambda 的初始负载性能非常糟糕，影响了用户体验。即使我用脚本让 lambdas 保持温暖，或者 AWS 提供了一个永远在线的 lambda，当函数扩展到处理负载时，启动问题仍然会遇到。与常规服务不同，一个函数一次只处理一个请求。它排队到一定数量，然后扩展。我们发现这不适合我们面向用户的服务。

```
CREATE TABLE IF NOT EXISTS Stream
(
    StreamId uuid NOT NULL,
    Version int NOT NULL,
    Type text NOT NULL,
    PRIMARY KEY (StreamId)
);

CREATE INDEX IF NOT EXISTS idx_stream_type ON Stream (Type); 
```

Enter fullscreen mode Exit fullscreen mode

其核心是，该表简单地跟踪每个流的当前版本。下面是使用此表要遵循的具体流程。

*   在执行业务逻辑之前，获取流的当前版本。
*   执行业务逻辑，生成新事件。
*   仅在以下情况下保存事件(在事务中)
    *   流表中的版本与您启动时的版本相同
    *   然后还将流表更新为最后保存的版本
    *   指明事件是否已保存。例如，返回真/假

这确保了编写人员知道他们何时试图保存冲突事件，并且他们可以返回一个错误、重试或业务规则定义的一些其他冲突解决过程。我们可能会默认错误，直到有理由这样做。

> ⚠️ **注来自未来**
> 这张表实际上并没有使它进入我们的实现。并发编写器已经由 StreamId +版本上的唯一键处理。并发写入者将计算相同的版本，并且其中只有一个会成功。更多信息请见评论。

#### 并发服务 vs 并发用户

至少，我们需要知道，从我们开始处理业务逻辑到我们试图保存事件，流没有发生变化。大多数情况下，流在处理之前被加载——以重建域的当前状态。所以我们可以使用最后一次加载事件的版本作为预期版本。然后在我们保存新事件时验证它是否仍然相同。这将检测写入同一流的并发服务之间的冲突。

然而，我们可以更进一步。*客户端*可以向我们发送他们提交请求时正在处理的数据版本。在保存事件时使用此版本作为预期版本，不仅可以检测服务之间的写冲突，还可以检测用户的更改何时会意外覆盖另一个用户的更改。

#### 那这个`Type`是什么？

`Type`为“流”的类型。例如，流类型可以是“Ordering”，而流的事件之一可以具有类型“OrderPlaced”。它完全是可选的，但可以用来帮助生成快照(下面将讨论)。它还可以支持将事件向下筛选到特定类型的流。类型上的索引应该没有写入性能影响，因为只有对表的更新在 Version 上。

### 快照表

当一个流变得非常大(可能超过 1000 个事件？)，加载和重放流以获得当前状态可能会变得太慢。常见的缓解方法是使用快照。我们不是每次都从头开始重建领域模型，而是偶尔重建领域模型状态的最新版本，并将其保存到数据库中。之后，为了加载流，我们首先获取快照，然后只获取快照版本之后的事件。这是支持这一观点的表格。

```
CREATE TABLE IF NOT EXISTS Snapshot
(
    StreamId uuid NOT NULL,
    Version int NOT NULL,
    Data jsonb NOT NULL,
    Revision int NOT NULL,
    PRIMARY KEY (StreamId)
); 
```

Enter fullscreen mode Exit fullscreen mode

应用程序代码应该在流变得“太大”时创建快照(这应该由请求时间等指标来确定)。快照还应该不时地重新创建(在 1000 多个事件之后),并且当对快照进行结构改变时——参见下面的修订。

#### 什么是改版？

领域模型会随着时间而变化。有时变化是这样的，最后的快照不会反序列化回新的域模型。避免这种情况的一个相对简单的方法是在您的域代码中的某个地方保持一个修订常量，并在您进行结构更改时增加它。加载快照时，如果版本不兼容，查询可以确保忽略它。

> ⚡:我讨厌在开发过程中增加容易被遗忘的步骤，比如在结构变更时增加修订号。如果你有更好的方法来处理这件事，我很乐意听听！

#### 如何保存快照？

最好的方法可能是拥有一个单独的快照服务，它按计划运行。它可以使用流表来识别具有大量事件的流。它还可以检查现有的快照版本，以找到需要更新的版本。在[构建事件存储](https://cqrs.wordpress.com/documents/building-event-storage/)中描述了一次完成这两项检查的查询。此外，可以检查快照修订，以便为结构更改重新创建快照。

现在，需要快照的流已被识别。下一步是调用适当的域模型代码，将流重放到当前状态。然后，该状态被保存到快照表中。小溪。类型可用于确定调用哪个流重播代码。如果您没有选择在流表中包含 Type 列，那么您也可以读取流的第一个事件。通常这些都是流的类型的指示。

#### 你可能不需要快照。

对于许多类型的应用程序，设计良好的流在其生命周期中不会累积大量事件。使用快照当然没有坏处，但是它们需要开发人员花时间来实现。并且它们总是可以在以后被添加。

## 我们完成了什么？

这些变化允许使用事件存储的服务以无共享的方式扩展。它们甚至可以是无状态的，在无服务器平台上使用。随着流大小的增长，它们还提供一致的加载性能。

### 数据库本身的伸缩呢？

我们还没有真正解决水平扩展事件数据库本身的问题。在一个多租户应用程序中，向这些表添加一个`TenantId`并使用一个分片解决方案，比如 [Citus Data](https://www.citusdata.com/) ，会非常容易。

我目前的多租户方法是基于模式的隔离。转换成分片方法并不困难，我可能最终会这样做。然而，我真的很喜欢模式隔离，所以我正在考虑使用一个简单的目录(例如 S3 上的一个文件)将租户映射到数据库实例。

**更新 2019-01-23**

下面的评论值得一读，尤其是和 [@damiensawyer](https://dev.to/damiensawyer) 的讨论。我得出的结论是，唯一需要的表是事件表。如果需要，可以在以后添加快照表。毕竟流表并不是真正必要的。事实上，这使实现变得相当复杂——我必须创建一个不那么小的存储过程来保持流更新，作为附加事件的一部分。所以我认为不值得。