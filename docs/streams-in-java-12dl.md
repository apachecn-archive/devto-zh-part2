# Java 中的流

> 原文：<https://dev.to/elcotu/streams-in-java-12dl>

大家好。

今年我致力于学习更多的 Javascript，但是我继续在我的桌面工作中使用 Java。

本周，我需要开发一个小功能来扩展我们使用的 ERP，导出一个文本文件以符合政府机构的要求。

为了做到这一点，并尝试使用一种更“功能化”的方法，我研究了 Stream 对象。

这是语言中的一项新功能。流是一个集合，它允许以声明方式进行数据处理。例如，我需要对一组项目求和。在数组列表的旧时代，这是我的代码:

```
BigDecimal total = BigDecimal.ZERO;
Iterator iterator = listOfValues.iterator();
while (iterator.hasNext()) {
    TotalSsn currentElement = iterator.next();
    if (currentElement.isChildrenOf(account)) {
    total.add(currentElement.getImporte());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Stream 对象的一点帮助下，代码看起来像这样:

```
BigDecimal total = BigDecimal.ZERO;

Optional<BigDecimal> totalOptional = listOfValues.stream().filter(y -> y.isChildrenOf(account)).map(TotalSsn::getImporte).reduce((a, b) -> (a.add(b)));
if (totalOptional.isPresent()) {
    total = total.get();
} 
```

Enter fullscreen mode Exit fullscreen mode

在第二种方法中，我使用:

# 流()

此方法返回一个流。这是 List 接口的一个方法，所以在遗留代码中获得一个流是非常简单的。

# 滤镜()

该方法将一个函数接口作为参数，并返回一个新的流，其结果是将该函数应用于原始流的元素。

# 地图()

这个方法允许你用另一个泛型类型生成一个新的流。在这种情况下，我将 TotalSsn 转换为 BigDecimal。

# 减少()

在这种情况下，允许计算流值的总和。

这整个 API，我觉得都是值得探讨的。

你好，