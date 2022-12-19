# 用 Java8 Lambda 从列表中过滤空值

> 原文：<https://dev.to/monknomo/filter-null-values-from-a-list-with-java8-lambda-351h>

Java 流的一个常见任务是清理输入数据，这样后面的步骤就可以轻松完成。最常见的清理步骤可能是从集合中移除空值。

溪流让一切变得简单:

```
myCollection.stream()
  .filter(Objects::nonNull)
  .do.what.you.need 
```

Enter fullscreen mode Exit fullscreen mode

与传统方法相比:

```
while(myCollection.remove(null));
// do what you need, but you better not need that original list, because it's gone...
myCollection.removeAll(Collections.singleton(null));
// do what you need, but you better not need that original list, because it's gone... 
```

Enter fullscreen mode Exit fullscreen mode

与流方法一样，这些方法简短而有趣，但与流方法不同，它们修改了原始列表。第一个例子也很慢。

我喜欢流方法，因为我可以在过滤器任务之后链接额外的任务，包括`map`。`sorted`、`reduce`还有更多！。我发现传统的命令式迭代方法不仅冗长，而且在概念上更难理解。