# 遇见雷夫斯

> 原文：<https://dev.to/adigiovanni/meet-reaves-aca>

Reaves 是我刚刚在 npm 上发布的一个包。它是[实体-属性-值模型](https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model)和[事件源模式](https://martinfowler.com/eaaDev/EventSourcing.html)的 Javascript 实现。它运行在 Node.js 上，由 Redis 提供支持。

简而言之，Reaves 允许您保存和检索属于由字符串 id 标识的实体的属性的当前和过去的字符串值。

这第一次发布是非常简约的。尽管如此，Reaves 已经支持值的*区分大小写*、*可空*和*唯一*约束，并且它实现了一系列有用的方法来[查询数据](https://adriano-di-giovanni.github.io/reaves/EntityAttribute.html)。

```
const { createEntityAttribute, CASE_SENSITIVE, NULLABLE, UNIQUE } = require('reaves')
const { generate } = require('randomstring')
const redis = require('redis')
const uuidv4 = require('uuid/v4')

const client = redis.createClient()
const entityName = 'player'
const attributeName = 'nickname'
const flags = CASE_SENSITIVE | NULLABLE | UNIQUE

createEntityAttribute(client, entityName, attributeName, flags, (err, playerNickname) => {
    if (err) {
        throw err
    }

    const entityId = uuidv4()
    const newValue = generate()
    const createdAt = Date.now()

    playerNickname.insert(entityId, newValue, createdAt, console.log.bind(console))
}) 
```

我为 Reaves 感到非常自豪，因为它是一个解决实际问题的不那么小的软件。我认为它很好地解决了这个问题，而且它不需要我们——你和我——再增加一项技术。

为了性能和原子性，核心函数用 Lua 编写。它们在运行时使用我的另一个 npm 包 [luaload](https://www.npmjs.com/package/luaload) 加载到 Redis 的脚本缓存中，该包也将多个脚本文件动态连接成一个。

出于生产或管理目的，我将使用 Reaves 实现一些依赖于实体属性的过去和现在值的特性。

而且对了，Reaves 是 *Redis 实体属性值 event sourcing* 的缩写。

你如何看待 Reaves 及其背后的理念？你发现任何缺点了吗？

请让我知道。这就是我想与你分享收获的原因。

谢了。