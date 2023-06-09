# Kotlin 中 JdbcTemplate 的流结果

> 原文：<https://dev.to/jillesvangurp/streaming-results-from-a-jdbctemplate-in-kotlin-474h>

最近，我在一个基于 Kotlin 的 Spring Boot 2.x 项目中从使用 Hibernate 过渡到使用 JdbcTemplate。我在另一篇文章中写下了原因和方法。我需要移动的一个存储库方法是这样的:

```
 @Query("...")
    fun streamUserIds(): Stream<String> 
```

Spring Boot 的一个优点是它做了正确的事情，生成了一个有用的实现，做了正确的事情。你可以做流，选项，列表等。JdbcTemplate 实际上没有任何类似的东西。

这是 JdbcTemplate API 中有点奇怪的遗漏，但是很容易修复。在谷歌搜索了一下之后，我找到了这个有点帮助的页面,里面有一个解决方案，几乎可以用，但不完全可以。但这让我走上了正轨。

因此，我决定分享我的实现，因为我认为它更简单、更好:

```
 fun <T> queryStream(sql: String, converter: (SqlRowSet) -> T?, args: Array<Any>): Stream<T> {
        val rowSet = jdbcTemplate.queryForRowSet(sql, *args)

        class RowSetIter : Iterator<T> {
            var current: T? = null

            override fun hasNext(): Boolean {
                if (current != null) {
                    return true
                } else {
                    if (rowSet.next()) {
                        current = converter.invoke(rowSet)
                        return true
                    }
                }
                return false
            }

            override fun next(): T {
                if (hasNext()) {
                    val retVal = current
                    current = null
                    return retVal!!
                } else {
                    throw NoSuchElementException()
                }
            }
        }

        val spliterator = Spliterators.spliteratorUnknownSize(RowSetIter(), Spliterator.IMMUTABLE)
        return StreamSupport.stream(spliterator, false)
    } 
```

这只是用一个简单的迭代器包装`SqlRowSet`。`SqlRowSet`是 JDBC `RowSet`的一个简单包装器，带有合理的异常处理，使得上面的内容不那么单调乏味。

我的实现修复了链接文章中的代码存在的一些问题:

*   退出条件错误，省略了最后一行。
*   我喜欢用`hasNext()`的方法来代替`next()`的方法，让`next`依赖`hasNext()`。这也消除了在第一行调用 next 的需要。我以前实现过一些迭代器，这似乎是一个很好的迭代器模式。
*   它试图对行集进行流处理。这实际上没有意义，因为这是一个代表 db 游标的低级对象，而你所做的只是返回同一个对象并对其调用`next()`。因此，我使用 lambda 将每一行转换成一个`T`。因此，无论你是映射某个实体还是仅仅提取字符串，它都是可行的。如果你真的只想遍历这些行并且不返回任何东西，你可以让`T`成为`Unit`。

另一个问题是，显然不能在流式传输之前关闭连接，因为它需要使用数据库游标来获得结果。因此，解决这个问题的正确方法是使用 TransactionTemplate，这样您就可以保持连接打开，直到您完成从 DB:
输出结果

```
transactionTemplate.execute {
    dao.queryStream("select user_id from table") { rs, _ ->
        // Map rows to a String
        rs.getString("user_id")
    }.forEach {
        // do something with each user_id
        println("User $it")
    }    
} 
```

无论您有 50 个用户还是 5000 万个用户 id，这都是可行的。如果需要的话，上面的代码应该很容易移植回 Java。我还将这段代码作为 Github gist 的评论分享，Github gist 的链接来自启发我的那篇文章。