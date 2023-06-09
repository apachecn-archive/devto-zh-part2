# 撕掉冬眠，回到 JDBC 本土

> 原文：<https://dev.to/jillesvangurp/ripping-out-hibernate-and-going-native-jdbc-1lf2>

几周前，我发现一些事务性数据库逻辑行为不正常。经过大量的摆弄，调试，去 WTF？！和您一样，我发现我遇到了一个与事务语义相关的问题:mysql 事务是不可序列化的，除非您经历了很多困难。

行为不当的逻辑是我实现的一个简单的逻辑，用来模拟一个简单的突变分类帐。这里的想法是，突变只有一个祖先，是不可变的，它们的 id 是几个东西的内容散列，包括祖先 id 和一个序列号。

逻辑非常简单:

*   查找最大序列号和相应的突变 id
*   用它来创造新的变异
*   将它插入表中
*   在单个事务中执行上述所有操作，以确保我读取的值没有过时，并插入该值。

除了事务在 Mysql 中不可序列化之外，这几乎是可行的。这意味着当你同时有多个突变时，它们各自找出相同的祖先，然后试图插入到数据库中。我们有适当的约束来防止这种情况。因此，事务因数据完整性异常而失败，并回滚，变异丢失。不是你想要的。

解决方案:捕获异常并重试。这就是 hibernate 成为我问题的一部分的原因。我们已经用春天的方式做了一些事情，在我们的逻辑上意味着`@Transactional`和进入`@Repository`的`@Entity`。Spring 做了很多 AOP 魔术来使@Transactional 工作，并与 hibernate 一起管理一个会话，该会话具有代表您的实体的代理对象。这是伟大的，直到它打破。然后就变成了反面。

这使得解决上述问题变得困难。首先，在事务中添加可序列化的隔离只会在 mysql 中产生死锁。它不起作用。这是 Mysql 的一个局限。如果你愿意，你可以欺骗它锁定表和行，但是祝你通过 JPA 成功。因此，这是一个捕捉异常、重新运行逻辑并希望它成功的问题。我很快发现这在`@Transactional`中是不可能的，因为在 AOP 代码中，异常发生在您的代码之外。这当然很烦人。解决方案，使用`TransactionTemplate`和一个 try catch。太好了，看起来它正在工作，只是下一个事务也失败了，下一个(我重试了 10 次)。原因:hibernate 没有关闭事务之间的连接，显然没有从数据库中重新读取值。它只是重用已经读取的值。我试着摆弄缓存设置，但从来没有真正弄清楚为什么这不起作用。

我花了几天时间试图让 spring 做到这一点，例如，千方百计地找到一个 entityManager，让它做我想做的事情。它一直在和我抗争。我毫不怀疑这是可以解决的，如果我再获得一个复杂的框架设计的博士学位，和任何认为拥有大量抽象类是个好主意的人一起思考，这些抽象类扩展了实现大量接口的抽象类。每当我开始看 Spring hibernate 代码时，我都会感到头疼。太复杂了。我承认，我不配，飞行员失误，等等。但最终的结果是我浪费了大量的时间试图让这个工作失败。如果我遗漏了什么，请在下面的评论中告诉我。

# 摆脱冬眠

在几天痛苦地试图保护 hibernate 中的现有投资并使其发挥作用(沉没成本谬误)之后，我最终放弃了，并做了我在这个项目的第一天就考虑过但不幸没有做的事情:使用 JdbcTemplate。于是，我创建了一个名为`fuckhibernate`的分支。我是那种在给分支机构取名字或传递信息时不隐藏自己感情的人。我此时的提交日志证明了我所经历的沮丧程度，也是一些有趣的读物。顾名思义，创建这个分支是为了完全摆脱 hibernate。我不想再处理这件事了。我花了大约一天的时间编写代码来完全消除它。比我浪费在修复破碎魔法上的时间要少得多。所以，这是一笔不错的投资。

在这个过程中，我将许多代码转换成了 Kotlin。这是我已经做了一段时间的事情。我写的所有新代码都是科特林。

我遵循的过程非常简单。我有经典的 spring 架构，其中有使用存储实体的仓库的服务。所有数据库的东西都在存储库中(非事务性的)，而事务性逻辑在服务中。你可以仔细阅读它是如何工作的。

我去了一个又一个仓库。对于每个存储库:

*   我用 JdbcTemplate 创建了一个新的 DAO 类，为存储库接口中的所有方法注入了存根方法，并将服务类切换到使用该方法。
*   删除实体上 hibernate 注释的所有痕迹，将其转换为 Kotlin 数据类，并稍微重构代码以使其更加 Kotlin 友好(例如，nullable vs optionals，正确使用属性，并使其不可变)。一旦放弃 hibernate，就不需要可变类了。这很棒，并且消除了很多复杂性。
*   然后我一个一个实现了存根。这很简单。
*   我删除了 Repository 类，去掉了任何 jpa 注释，并敲打 DAOs，直到所有测试再次通过。
*   在我们的服务类中去掉@Transactional，改用`TransactionTemplate`。

# 举例

例如，我有一个小的 KV 存储表，我也使用相同的方法移植了它。它是为了用一些键存储小的 json 对象。有一个服务、一个存储库和一个实体。

```
import java.time.Instant

data class KVEntry(
    val id: String = "",
    val data: String = "",
    val createdDate: Instant = Instant.now(),
    val lastModifiedDate: Instant = Instant.now(),
    val contentHash: String = "default"
) 
```

我添加了`contentHash`以在将来添加基于 Etags 的乐观锁定，只有当 contenthash 正确时，更新才会成功。这可以避免人们在他们的副本过时时覆盖这些值。我已经针对不同的数据库实现了一些 kv 存储，这是一个有用的特性。请注意，您可以针对任何能够存储键/值对的对象轻松地实现这一点。我过去做过 redis、mysql、postgresql、voldemort db、couchdb、solr 和 elasticsearch 实现。这是在一个简单的服务类中使用的:

```
import io.inbot.ethclient.irn.IRN
// https://github.com/Inbot/inbot-utils
import io.inbot.utils.HashUtils
import org.springframework.stereotype.Component
import java.time.Instant

@Component
/**  * Simple KV store.  */
class KVService(
    val kvDao: KVDao
) {
    fun get(irn: IRN): KVEntry? {
        return kvDao.getById(irn.stringValue())
    }

    fun list(prefix: String): List<String> {
        return kvDao.findByPrefix(prefix)
    }

    fun put(irn: IRN, value: String) {
        val newEntry =
            KVEntry(irn.stringValue(), value, Instant.now(), Instant.now(), HashUtils.md5(irn.stringValue(), value))
        return kvDao.save(newEntry)
    }

    fun delete(irn: IRN) {
        kvDao.deleteById(irn.stringValue())
    }
} 
```

这个服务类的主要目的是，我想通过使用 IRN (inbot 资源名)在我们的键上强加一些结构，这是我们产品中的一个东西。我们也在这个类中处理内容散列。除了做数据库工作之外，DAO 完全没有逻辑可言。它的灵感来自亚马逊的 ARN。你可以使用你自己的或者仅仅使用字符串，也许添加一些方法来处理 json 序列化，等等。因为所有的 db 交互都是一个语句，所以这里不需要事务，因为默认情况下 autocommit 为 true。还缺少带有乐观锁定的更新方法的实现。

服务类驱动 DAO:

```
import org.springframework.jdbc.core.JdbcTemplate
import org.springframework.jdbc.core.query
import org.springframework.stereotype.Component

@Component
class KVDao(val jdbcTemplate: JdbcTemplate) {
    fun getById(id: String): KVEntry? {
        return jdbcTemplate.query(
            "SELECT id, data, created_date, last_modified_date, content_hash FROM eth_kvstore WHERE id = ?",
            id
        ) { rs, _ ->
            KVEntry(
                rs.getString("id"),
                rs.getString("data"),
                rs.getTimestamp("created_date").toInstant(),
                rs.getTimestamp("last_modified_date").toInstant(),
                rs.getString("content_hash")
            )
        }.firstOrNull()
    }

    fun findByPrefix(prefix: String): List<String> {
        return jdbcTemplate.query(
            "SELECT id FROM eth_kvstore WHERE id like CONCAT('%',:id, '%')",
            mapOf("id" to "id")
        ) { rs, _ ->
            rs.getString("id")
        }
    }

    fun save(newEntry: KVEntry) {
        // do an upsert
        jdbcTemplate.update(
            "REPLACE INTO eth_kvstore (id, data, created_date, last_modified_date, content_hash) VALUES (?,?,?,?,?)",
            newEntry.id,
            newEntry.data,
            newEntry.createdDate,
            newEntry.lastModifiedDate,
            newEntry.contentHash
        )
    }

    fun deleteById(id: String) {
        jdbcTemplate.update("DELETE FROM eth_kvstore WHERE id = ?", id)
    }
} 
```

这就是事情变得有趣的地方。这是 hibernate 通常为您生成的内容。是的，写这个是工作，但是没有注释，没有复杂性，没有魔力，等等。也没那么糟糕。我确信有办法通过一些反射黑客技术和更多的 spring good 来进一步改进这一点。Spring 5 还捆绑了几个有用的 Kotlin 扩展方法。还有命名参数，我还没有用到。所以，这不是简单的最终答案。但从根本上说，这对我来说已经足够好了。它工作，完成工作，而且没有冬眠魔法。

# 拜拜冬眠

在我给了我们所有的实体相同的待遇后，我简单地把依赖关系中的`org.springframework.boot:spring-boot-starter-jpa:2.0.2.RELEASE`换成了`org.springframework.boot:spring-boot-starter-jdbc:2.0.2.RELEASE`。这消除了 hibernate 和相关的启动代价。通过去掉 hibernate，我从 40 秒的构建中节省了大约 10 秒。谢天谢地。

# 解决一致性问题

当然，仅仅摆脱 hibernate 并没有解决我的问题。但是这使得修复变得非常容易。当然，第一步是生成一个测试，重现一个执行器和几个试图同时产生突变的线程的问题。可靠地重现了问题。

然后，我用了大约 2 分钟的时间完成了这个丑陋的方法，它包装了原来的方法(通过一些递归来制作一个更漂亮的版本):

```
 fun mutateBalance(
        userId: String,
        transactionIdentifier: String,
        type: MutationType,
        description: String,
        createdDate: Instant,
        sourceIrn: IRN,
        updateLambda: (BalanceMutation) -> Unit
    ): BalanceMutation? {
        var tries = 0
        while (tries < 10) {
            try {
                val result = mutateBalanceInternal(
                    userId,
                    transactionIdentifier,
                    type,
                    description,
                    createdDate,
                    sourceIrn,
                    updateLambda
                )
                if (tries > 0) {
                    LOG.warn("succesful transaction after $tries tries")
                }
                return result
            } catch (e: DataIntegrityViolationException) {
                // if we violate constraints, retry
                if (tries++ < 10) {
                    // ugly sleep with some magical numbers to give the other transactions a chance to do their thing.
                    Thread.sleep(RandomUtils.nextLong(100, 1000))
                } else throw e
            }
        }
        // in our test we only get this if we are starved for db connections and are doing hundreds of concurrent transactions
        throw IllegalStateException("failed after $tries")
    }

    fun mutateBalanceInternal(
        userId: String,
        transactionIdentifier: String,
        type: MutationType,
        description: String,
        createdDate: Instant,
        sourceIrn: IRN,
        updateLambda: (BalanceMutation) -> Unit
    ): BalanceMutation? {

        return transactionTemplate.execute {
            // pseudo code below, real code omitted for brevity
            val previousMutation = DAO.getLatestMutationForUser(userId)
            // create newMutation that refers the previous one
            // this fails with a DataIntegrityException if another 
            // transaction uses the same previousMutation concurrently.
            DAO.save(newMutation)
        }
    } 
```

这完全解决了我们的问题。基本上所有的测试运行我都看到一些重试，表明问题已经存在并得到解决。有时需要更多的尝试。如果你用力过猛，你会耗尽你的连接和线程池，并且在 10 次重试后开始失败。对于可序列化的事务，不需要这些代码。可悲的是，在 2018 年，mysql 和可序列化事务是一个问题，所以需要这些代码。出于这个原因，我实际上正在考虑 cockroachdb，但我喜欢在 amazon RDS 中托管 mysql，并且备份有点太多了。

# 结论

当然，我对冬眠有点苛刻。这是故意的，因为我 1)对此非常沮丧，2)试图激怒我的读者。这不是针对个人的；-).Hibernate 对于简单的东西当然很好，很多人成功地使用了它，并且专门使用它。然而，在我见过的所有使用它的项目中，我观察到一些事情:

*   使用它的人似乎不理解 SQL，并且正在制造许多性能瓶颈，例如急切加载(就是不要)、失控连接，以及 JPA 通常只是创建数百个愚蠢的查询。我爱 SQL 我一直在用它。对我来说，JPA 是紧身衣。
*   对象阻抗不匹配导致人们过度设计他们的 DB 模式。抱歉，继承不是数据库里的东西。真的需要 20 桌吗？你实际上选择了多少列？
*   人们对事务的界限知之甚少，基本上人们只是到处复制粘贴。工作时很好，坏掉时很可怕。
*   它总是在某个时候坏掉，然后 WTFs/分钟速率真的飙升，直到有人像我一样坐下来把事情理顺。这不是有趣的工作。通常症状是类似于“它很慢”，“为什么这个测试突然不起作用”等。

底线是你不需要休眠。我认为在 Spring 中用`TransactionTemplate`来处理事务比用@Transactional 更安全，因为你可以更容易地捕捉异常，访问事务状态，并且你不依赖 AOP 的魔力(或者如果你调用同一个类中的方法，当它们发生时会有有趣的错误)。Spring 的`JdbcTemplate`很好用，从 Spring 3.x 开始就有了(我用 Spring 大概 10 年了，不是新手)。科特林去掉了许多赘言。Kotlin 不可变数据类绝对是我想要的所有数据库实体。其他好处:不必担心实体管理器、hibernate 会话和实体代理的怪异、hibernate 缓存和混淆的事务行为(技术上是 Spring 而不是 hibernate)、将原生 sql 映射到 JPA 自己的查询语言等。我喜欢言出必行的代码。