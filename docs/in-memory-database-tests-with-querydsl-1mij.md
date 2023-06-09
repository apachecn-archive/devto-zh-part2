# 使用 Querydsl 进行内存数据库测试

> 原文：<https://dev.to/brightdevs/in-memory-database-tests-with-querydsl-1mij>

编写测试是软件工程师的一项重要技能。我曾经写过许多非常集中的、狭隘的单元测试。然而，我经常发现这样的测试阻碍了重构，并且几乎无助于捕捉回归。这些问题是由我糟糕的设计选择引起的，还是单元测试固有的，这不是这篇文章的重点。然而，事实是现在我倾向于编写更粗粒度的集成风格的测试。这种方法有一个缺点:速度。例如，与使用一个假的存储库实现相比，使用 Hibernate 和一个成熟的数据库相对较慢。今天我写的是以一种有助于测试的方式使用 [Querydsl](http://www.querydsl.com/) 抽象数据库访问。

[![test](img/b6849de29c595813289de5b00249c272.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fi9-mc-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vbbymak91lui0hlm9bny.jpg)

# Querydsl 牛逼

[Querydsl](http://www.querydsl.com/) 是一组库，顾名思义，它提供强类型的领域特定语言来执行查询。 [Querydsl](http://www.querydsl.com/) 支持多种数据访问技术，如 JDBC、Hibernate、JDO。
kot Lin 中的以下示例说明了如何使用基于实体类生成的 DSL 通过 JPA 接口找到一些实体:

```
val queryFactory: JPAQueryFactory = ...
val userEmailToSearch = "alamakota@gmail.com"
val user = queryFactory.query()
    .from(QUser.user)
    .where(QUser.user.email.eq(userEmailToSearch))
    .select(QUser.user)
    .fetchOne() 
```

Enter fullscreen mode Exit fullscreen mode

一个重要的可用选项是提供 POJO 集合和 beans 集成的[集合](https://github.com/querydsl/querydsl/tree/master/querydsl-collections)模块。Kotlin 中的以下示例显示了如何查询用户列表:

```
val users = listOf(userAlan, userBob, userAlice)
val user = CollQuery<Nothing>()
    .from(QUser.user, users)
    .where(QUser.user.email.eq(userEmailToSearch))
    .select(QUser.user)
    .fetchOne() 
```

Enter fullscreen mode Exit fullscreen mode

# 把复杂的东西抽象掉

由于 Querydsl 提供的公共接口，上面的例子看起来很相似。然而，虽然默认的 DSL 非常有用，但我发现在大多数情况下它有点冗长。就此而言，让我们定义一个稍微简单一点的接口，它将允许在给定一些标准的情况下找到实体

```
interface EntityQueries {
    fun <TQEntity : EntityPath<TEntity>, TEntity : Any> findFirst(
        qEntity: TQEntity, 
        where: (TQEntity) -> Predicate? = { null }, 
        orderBy: ((TQEntity) -> OrderSpecifier<*>?) = { null }): TEntity? 
}

val queries:EntityQueries = ....

val ala = queries.findFirst(QUser.user, where = { it.email.eq("ala@gmail.com") })
val latestUser = queries.findFirst(QUser.user, orderBy = { it.created.desc() }) 
```

Enter fullscreen mode Exit fullscreen mode

上面的接口允许我们以更简洁的方式表达常用的查询。

# 定义生产实现

使用 Querydsl，实现`EntityQueries`接口非常容易。首先，将实际数据访问技术的生产实现委托给 JPA:

```
class QueryDslDomainQueryFactory(private val queryFactory: JPAQueryFactory) : EntityQueries {
    override fun <TQEntity : EntityPath<TEntity>, TEntity : Any> findFirst(qEntity: TQEntity, where: (TQEntity) -> Predicate?, orderBy: (TQEntity) -> OrderSpecifier<*>?): TEntity? {
        return queryFactory.query()
            .from(qEntity)
            .where(where(qEntity))
            .apply { orderBy(qEntity)?.let { this.orderBy(it) }  }
            .select(qEntity)
            .fetchFirst()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面让我们使用`EntityQueries`接口来代替 JPA，例如像这样的 Spring 控制器:

```
@RestController
class UsersController(private val queries: EntityQueries) {
    @GetMapping("/users")
    fun getByEmail(@RequestParam email: String) = queries.findFirst(QUser.user, where = { it.email.eq(email) })
} 
```

Enter fullscreen mode Exit fullscreen mode

Spring 推荐的抽象查询技术细节的方法之一是使用[存储库接口](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods)例如:

```
interface UserRepository : Repository<User, Long> {
  fun findByEmail(String email): User?
} 
```

Enter fullscreen mode Exit fullscreen mode

这样的接口会被 Spring runtime 神奇地实现并放到应用程序上下文中。这种方法初看起来很吸引人，因为我们不需要实现接口。然而，有多个问题:

*   需要一个应用程序上下文，这反过来会导致启动缓慢
*   没有编译时检查
*   没有 IDE 的特殊支持，重构会更加困难
*   如果没有仔细的文档讲座，很难弄清楚实际的行为(例如，如果有多个用户使用同一个电子邮件，会发生什么？)

通过电子邮件查找用户的`EntityQueries`调用几乎和`findByEmail`一样可读，但是没有上面列出的任何缺点。封装更复杂的过滤逻辑可以用一个简单的扩展方法或者一个更复杂的[规范模式](https://en.wikipedia.org/wiki/Specification_pattern)来完成。

# 在测试中使用内存数据库

我们可以使用 Spring test helpers 来简化涉及应用程序上下文的编写测试，让我们注入例如`UsersController`实例来调用它的方法。然而，相对而言，这样的测试运行起来非常慢，因此导致反馈回路慢得多。幸运的是，使用 POJO 内存集合很容易实现`EntityQueries`抽象。

```
class InMemoryEntityQueries : QueriesBase(), EntityQueries {
    val entities = mutableMapOf<Class<*>, MutableList<*>>()

    override fun <TQEntity : EntityPath<TEntity>, TEntity : Any> findFirst(qEntity: TQEntity, where: (TQEntity) -> Predicate?, orderBy: (TQEntity) -> OrderSpecifier<*>?): TEntity? {
        val entities = entities.getOrPut(qEntity.type, { mutableListOf<TEntity>() }) as List<TEntity>
        return CollQuery<Nothing>()
            .from(qEntity, entities)
            .where(where(qEntity))
            .apply { orderBy(qEntity)?.let { this.orderBy(it) } }
            .select(qEntity)
            .fetchFirst()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的实现看起来几乎和生产中的一模一样。我们当然可以尝试提取通用代码，让事情变得更干巴巴。然而，最重要的观察是，我们将重要的过滤和排序逻辑委托给了 Querydsl 实现。这可以增加我们的信心，即伪实现的行为与生产实现相同，唯一的区别是实际的实体存储。

给定上面的实现，我们现在可以很容易地替换`UsersController`依赖项，并将其实例化为一个常规的 POJO:

```
class UsersControllerTests {
    val db = InMemoryEntityQueries()
    val controller = UsersController(db)

    @Test
    fun canFindByEmail(){
        db.entities[User::class.java] =  listOf(User(email = "ala@gmail.com"), User(email = "ola@gmail.com"))

        controller.getByEmail("ola@gmail.com").email.shouldEqual("ola@gmail.com")
        controller.getByEmail("peter@gmail.com").shouldEqual(null)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 关于内存实现的说明

上面的`EntityQueries`界面显然是简化版。最重要的缺失部分是保存实体的能力。然而，考虑到内存中的实现，这并不难实现。例如，我们可以利用我们所有的实体都被标记为 JPA Persistence annotations 这一事实来找到一个标记为`@Id`的字段，生成 id 并根据`entities`变量的内容对其进行赋值。另一种方法是用专用接口标记所有实体，例如

```
interface HasId<TId> {
    var id: TId
} 
```

Enter fullscreen mode Exit fullscreen mode

可以在内存实现的`save`方法中检查实现`HasId`的实体，并为其分配唯一的标识符，例如:

```
fun <TEntity: HasId<Long>> save(entity: TEntity) {
    val entities = entities.getOrPut(entity.javaClass, { mutableListOf<TEntity>() }) as List<TEntity>
    if(entity.id == null){
        entity.id = (entities.map { it.id }.max() ?: 0) + 1
    }
    entities += entity
} 
```

Enter fullscreen mode Exit fullscreen mode

按照上面的方法，我们可以很容易地添加缺失的操作，例如删除一个实体，这反过来允许我们编写更多的完全在内存中运行的测试。值得注意的是，使用内存数据库实现最适合于获取、保存或更新一个或多个实例的查询。一旦我们需要使用数据库技术固有的特性，例如 SQL 中的连接，我们就可以更好地连接到真正的数据库。虽然 Querydsl collections 模块支持连接和分组操作，但内存中的实现通常不等同于数据库中的实现，尤其是在处理`null`值时。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)