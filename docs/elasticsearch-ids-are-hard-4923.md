# 弹性搜索-id 很难

> 原文：<https://dev.to/jmlw/elasticsearch-ids-are-hard-4923>

[![Elasticsearch - IDs are hard](../Images/9cf2c87f1a1232536c1489e531c74b67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LikRpIpB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.joshmlwood.com/content/images/2018/07/elasticsearch-2.png)

有时 RTFM(阅读 f******手册)确实是最好的解决方案，但是当快速和敏捷地构建时，并不总是有时间阅读手册的每一页。

我最近了解到，Elasticsearch(碰巧还有 Amazon DynamoDB)对文档 id 进行了限制。我发现这一点是因为生成的文档 id 使用了从 DynamoDB 文档到 Elasticsearch 文档的映射。对于 Elasticsearch，文档 ID 的限制是 512 *字节*。如果要创建文档 id，请确保考虑到此限制。

具体来说，遇到的错误是`id is too long, must be no longer than 512 bytes but was: 513`。看看 GitHub 上的 [Elasticsearch 源代码，或者更具体地说是 IndexRequest.java](https://github.com/elastic/elasticsearch)的[类，这个错误是如何产生的就相当清楚了。索引请求将验证正在处理的文档，以确保它符合 Elasticsearch 的内部约束，如果不符合，它将返回违反约束的描述性错误。](https://github.com/elastic/elasticsearch/blob/d56de9890d895cd3038aa12c6d320512f6e88b9c/server/src/main/java/org/elasticsearch/action/index/IndexRequest.java#L182)

这里有两个我在工作中经常使用的语言的例子，Python 和 Java:

```
def get_len_bytes(a_string):
    bytes_of_a_string = bytes(a_string, 'utf-8')
    return len(bytes_of_a_string)

public int getLengthBytes(String aString) {
    byte[] utf8Bytes = aString.getBytes("UTF-8");
    return utf8Bytes.length;
} 
```

为了解决这个问题，我和我的团队讨论了一些可能的解决方案，允许我们继续保存这些文档，即使生成的 id 太长。我们提出的一些可能的解决方案是:

*   对文档 ID 做一个散列(这将主要保证惟一的键，并且文档 ID 仍然是幂等的)
*   截断文档 ID(不太理想，因为有可能生成重复的文档 ID)
*   拒绝文档 ID 过长的文档

最后我们决定采用第三种方案。对于我们的用例来说，超过 512 字节是不常见的。因此，我们可以采用这种幼稚的方法，在将来的某个时候不再处理太长的 id。

如前所述，我们甚至发现 AWS 管理的 NoSQL 文档库 DynamoDB 的大小限制。DynamoDB 限制在[分区&排序键限制](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html#limits-partition-sort-keys)中列出。

在这种情况下，我们遇到的异常是由 DynamoDB 抛出的一般异常，`ValidationException`。仔细看看这个异常，它与`One or more parameter values were invalid: Aggregated size of all range keys has exceeded the size limit of 1024 bytes (Service: AmazonDynamoDBv2; Status Code: 400; Error Code: ValidationException; Request ID: SOME_AWS_REQUEST)`相似。基本上，这意味着如果你正在写一个单一的记录，那么范围键太长，应该缩短或记录扔掉。

然而，当您尝试使用`batch_write_item` (Python)或`batchWriteItem` (Java)时，您很可能会看到这个错误。这里，错误意味着给定记录列表，所有记录中所有范围键的总字节数大于 1024 字节，因此无法处理请求。哦，不要忘记，DynamoDB 中的批处理操作最多只能处理 100 个项目，所以如果您希望范围键的大小相似，那么每个范围键大约有 120 个字节。

我想出了一个非常糟糕的解决方案来处理这两种情况。首先，我构建了一个`chunk`函数，它接受一个事物列表和一个`chunk_size`，然后返回一个列表列表，其中嵌套列表的长度最多为`chunk_size`。“你所说的‘所有范围键的总大小’错误是怎么回事...?"在这种情况下，来自前一个函数的每个块都被传递给一个`chunk_by_bytes`函数。给这个函数一个要分块的项目列表，一个要分块的字段名，以及给定列表中该字段的所有值的最大串联大小。它返回一个或多个列表的列表，其中给定字段的连接不超过给定的大小。这种方法足以解决所看到的错误，除了单个记录太大的情况。但是对于这些情况，数据只是被丢弃，并被注销，以便以后可以查看。

所以这只是导致另一个 RTFM 时刻。分区键(散列键)最多为 2048 字节，而排序键(范围键)最多为 1024 字节。不要忘记 DynamoDB 项目最大只能有 400 千字节，这包括 UTF-8 编码的属性名称。记住这一点很重要，特别是如果您试图将整个 Google Vision API 结果保存到一个 DynamoDB 记录中，并且只是吞下异常而不报告它们，正如我的一位同事最近在构建原型时发现的那样。

# 总之

你接触的每一项技术都会对你的数据施加限制。您必须努力确保您的数据能够符合这些限制，或者确保您的数据能够以某种方式编码以符合这些限制。如果您采用编码数据的方式，那么您的应用程序将很容易解码。但缺点是这降低了搜索或查询编码数据的能力。这也意味着，如果人类需要与数据进行交互，那么您的记录将更加难以处理和用于调试。您的用例以及对可调试性的需求将决定数据是否应该或者能够被重新格式化、拒绝或者编码，以及哪种方法最适合您、您的团队以及您的应用程序。就我个人而言，我会尽可能地保持透明，只保留“好的”数据，格式要便于机器和人类阅读，也便于模仿。其他任何事情都可能使调试和可观察性变得困难或不可能。