# Java 8 流备忘单

> 原文：<https://dev.to/monknomo/java-8-stream-cheatsheet--oad>

*最初发布于[www.gunnargissel.com](https://www.gunnargissel.com/java8-stream-cheatsheet.html)T3】*

流是处理集合中对象的一种方式。

流允许一个人说他们希望最后得到的东西。相比之下，命令式方法要求一个人说*如何*收集他们最后想要的东西。

流也允许简单的多线程。

# 基本流方法

有几个控制流交互的基本方法。这些方法背后的一般思想是操纵对象的集合，以便只剩下您想要的对象。

如果我不得不选择处理一个流不可或缺的方法，我会说`Map`、`Filter`和`Collect`都在其中。

**过滤器**
过滤器取一个`Predicate`，只留下`Predicate`为真的值。

**Map**
Map 取一个`Function`，将流中的值从一种类型转换为另一种类型

**排序**
排序取一个`Comparator`，按照`Comparator`中指定的排序对流中的值进行排序。

**Collect**
`Collect`停止流并将流中的值放入某种`Collection`中，就像一个`List`。

**toArray**
`toArray()`停止流并返回流中包含的元素的数组。

# 发现者

使用集合时，一个常见的任务是从流中获取元素。这些通常用在`Filter`和`Map`之后，以保证您找到的元素匹配某些标准并且是首选类型。

**findanny()**
`findAny()`返回一个包含流中元素的`Optional`(如果流为空，则返回空值)。这里不尊重顺序，尤其是在并行流的情况下。

**findFirst()**
`findFirst()`返回一个包含流中第一个元素的`Optional`(如果流为空，则返回 nothing)。使用这种方法时会考虑顺序

**max(比较器比较器)**
`max`返回给定`Comparator`的流的最大元素

**min(比较器比较器)**
`min`返回给定`Comparator`的流的最小元素

# 赛者

另一个常见的任务是确定对象是否满足某些给定的标准。这些“匹配器”方法指示流中的一些、没有或所有对象是否满足给定的标准。

**allMatch(谓词预测)**
如果流中的所有元素都与`Predicate`匹配，则返回`true`

**anyMatch(谓词预测)**
如果流中有任何元素匹配`Predicate`，则返回`true`

**noneMatch(谓词 pred)**
如果流中没有元素与`Predicate`匹配，则返回`true`

# 流修饰符

有时，一个流并不完全是“正确的”流。这些方法创建的新流与旧流的特征略有不同，无论是不同的长度、不同的起点，还是只包含唯一对象的保证。

**distinct()**
`distinct()`创建一个只包含不同元素的新流(基于`.equals`

**limit(int maxSize)**
`limit(maxSize)`通过截断原始流使其长度不超过 maxSize 来创建新流。

**skip(long n)**
跳过流的前 n 个元素，并从剩余的元素中创建新流

**sorted()**
创建一个新的流，其中的元素按照自然顺序排序

**排序(比较器)**
创建一个新的流，其中的元素根据`Comparator`排序

# 流表征器

在处理集合时，另一项常见任务是确定集合的整体特征。stream api 提供了一种计算给定流有多大的方法。

**count()**
`count()`统计流中元素的数量

**如果你喜欢这个，请访问[我的编程博客](https://www.gunnargissel.com)以获得更多提示和技巧**

## 学分

感谢唐·拉旺热提供的一条[溪流](https://flic.kr/p/5quJsw)的图片