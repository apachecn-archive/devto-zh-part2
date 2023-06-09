# 使用 GenStage 和 EventBus 将事件数据持久化到 Postgres

> 原文：<https://dev.to/mustafaturan/persisting-event-data-to-postgres-using-genstage-and-eventbus-oi9>

消费 EventBus 事件的方法之一是实现 GenStage 消费者。GenStage 通过可配置的工作器轻松处理背压。`event_bus_postgres`库使用 GenStage 通过批量插入将`event_bus`事件保存到 postgres DB。

#### 工作原理

```
+-----+
|     |                                         GEN STAGE
|     |        EVENTBUS      +------------------------------------------+
|     |        CONSUMER      |                   +---+                  |
|     |        +-----+       |                   |   |                  |
|     |        |     |       |                   |   |          +---+   |
|     |        |  E  |       |                   |   |          |   |   |
|     |        |  v  |       |                   |   |          |   |   |
|     |        |  e  |       |                   |   |          |   |   |
|  E  |        |  n  |       |                   | E |          |   |   |
|  l  |        |  t  |       |  +-------+        | v |          |   |   |
|  i  | topic  |  B  |  topic   |       |        | e |          |   |
|  x  |   +    |  u  |    +     |   Q   |        | n |          | B |       +--+
|  i  |event_id|  s  | event_id |   u   |   ask  | t |    ask   | u |       |  |
|  r  |------->|  .  |--------->|   e   |<-------|   | <--------| c | BATCH |  |
|     |        |  P  |          |   u   |------->| M | -------->| k |------>|DB|
|  E  |        |  o  |          |   e   |   pull | a |    pull  | e | INSERT|  |
|  v  |        |  s  |          |       |        | p |          | t |       |  |
|  e  |        |  t  |       |  +-------+        | p |          |   |   |   +--+
|  n  |        |  g  |       |  GENSTAGE         | e |          |   |   |
|  t  |        |  r  |       |  PRODUCER         | r |          |   |   |
|  B  |        |  e  |       |                   |   |          |   |   |
|  u  |        |  s  |       |                   |   |          |   |   |
|  s  |        +-----+       |                   |   |          |   |   |
|     |<-----------------------------------------|   |          +---+   |
|     |                      |    fetch_event/1  |   |         CONSUMER |
|     |                      |                   |   |                  |
+-----+                      |                   +---+                  |
                             |                  CONSUMER                |
                             |                  PRODUCER                |
                             +------------------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

#### 组件

**EventBus**
消息总线为仙丹；它向`topic`用户发布`event_id`和`topic`数据。

**EventBus。Postgres**
消息总线事件消费者；它将`event_id`和`topic`推到`EventBus.Postgres.Queue`

**队列**
`GenStage`监制；这是一个简单的队列实现

**EventMapper**
`GenStage`生产者-消费者；它从`EventBus.Postgres.Queue`拉出/出列，从`EventBus`获取原始事件，然后将数据转换为`Ecto`模型。

**斗**
消费`GenStage`；它从`EventBus.Postgres.EventMapper`中拉出/出列数据，并向 Postgres DB 中批量插入数据。

源代码:[https://github.com/otobus/event_bus_postgres](https://github.com/otobus/event_bus_postgres)