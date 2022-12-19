# Apache HBase - REST API -原子操作

> 原文：<https://dev.to/risdenk/apache-hbase---rest-api---atomic-operations-5g58>

大声喊出来给搞清楚下面大部分素材的@德泉辰。

## 概述

[Apache HBase](https://hbase.apache.org/) 提供对大型数据集执行实时随机读/写访问的能力。HBase 构建在 [Apache Hadoop](https://hadoop.apache.org/) 之上，可以扩展到数十亿行和数百万列。Apache HBase 的功能之一是一个 [REST 服务器](https://hbase.apache.org/book.html#_rest)，以前叫做 [Stargate](https://wiki.apache.org/hadoop/Hbase/Stargate) 。这个 REST 服务器提供了从任何编程语言与 HBase 交互的能力。当特性被添加到 HBase 中时，它们在 REST API 中实现。

## Apache HBase 和原子操作

Apache HBase 中的原子操作非常重要，因为它减少了客户机和服务器之间的往返调用。如果有多个客户端，它还可以防止复杂的锁定。两个原子操作(`checkAndPut`和`checkAndDelete`)被添加到 REST 服务器，作为 [HBASE-4720](https://issues.apache.org/jira/browse/HBASE-4720) 的一部分。这些原子操作是 REST 服务器中未记录的特性之一。自 2012 年 11 月以来，这一直是一个开放的 HBase JIRA， [HBASE-7129](https://issues.apache.org/jira/browse/HBASE-7129) 。最近，我的团队发现了原子操作功能是如何工作的，并将向 Apache HBase 社区提供一个补丁。

## 使用 Apache HBase REST API

Apache HBase REST API 历史上在两个地方有记载。Apache HBase 参考指南和 T2 JavaDocs。这些文档大部分被移到了 Apache HBase 当前版本的参考指南中。

REST API 支持多种不同的格式:

*   纯文本- `application/octet-stream`
*   XML - `text/xml`
*   JSON—`application/json`
*   协议缓冲区- `application/x-protobuf`

这些格式中的每一种都可以被指定为`Accept`标题的一部分。这篇博客文章将关注 XML 和 JSON，因为它们最容易直接使用。

许多 Apache HBase REST API 端点需要使用 [`base64`编码](https://en.wikipedia.org/wiki/Base64)。`base64`编码确保数据可以毫无问题地跨 REST 传输。请记住，换行符和其他字符会影响`base64`的输出。`checkAndPut`和`checkAndDelete`都要求检查值完全匹配，所以要小心避免额外的字符。

当使用 Apache HBase REST APIs 时，URL 中的文字值通常必须与请求体中的`base64`编码值相匹配。这篇博客中的例子展示了哪些值需要被编码。

## 示例 Base64 编码值

下面的`base64`编码值被用在这篇博文的例子中。

| 文字文本 | Base64 编码 |
| --- | --- |
| `rowkey` | `cm93a2V5` |
| `columnfamily:qualifier` | `Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==` |
| `checkvalue` | `Y2hlY2t2YWx1ZQ==` |
| `newvalue` | `bmV3dmFsdWU=` |

## Apache h base REST API-checkAndPut

`checkAndPut`检查单元格最新版本的值，如果有匹配，将新数据放入同一单元格。

### 检查并输出单个限定符

这个`checkAndPut`调用将检查请求体中指定的特定限定符值，并将请求体中指定的限定符值放入 URL 中指定的 rowkey。下面是 HTTP 方法和端点，后面是一个带有解释的示例请求体。接下来是 XML 和 JSON 的具体`curl`示例。

```
# HTTP Method and Endpoint
PUT http://localhost:8084/namespace:table/rowkey/?check=put

# Example XML Request Body with Explanation
<CellSet>
  <Row key="Base64 Encoded RowKey">
    <Cell column="Base64 column family : qualifer">Base64 new value</Cell>
    <Cell column="Base64 column family : qualifer">Base64 check value</Cell>
  </Row>
</CellSet> 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:text/xml**

```
curl -i -H 'Accept: text/xml' \
  -XPUT 'http://localhost:8084/namespace:table/rowkey/?check=put' \
  -d '
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <CellSet>
    <Row key="cm93a2V5">
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">bmV3dmFsdWU=</Cell>
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">Y2hlY2t2YWx1ZQ==</Cell>
    </Row>
  </CellSet>' 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:应用/json**

```
curl -i -H 'Accept: application/json' \
  -XPUT 'http://localhost:8084/namespace:table/rowkey/?check=put' \
  -d '
  {
    "Row": [
      {
        "key": "cm93a2V5",
        "Cell": [
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "bmV3dmFsdWU="},
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "Y2hlY2t2YWx1ZQ=="}
        ]
      }
    ]
  }' 
```

Enter fullscreen mode Exit fullscreen mode

## Apache hbase rest API-check anddelete

`checkAndDelete`检查单元格的值，如果匹配，删除特定版本的限定符、限定符的所有版本、列族或行。

### 检查并删除限定符单个版本

这个`checkAndDelete`调用将检查请求体中指定的特定限定符值，并删除 URL 中指定的限定符的单一版本。下面是 HTTP 方法和端点，后面是一个带有解释的示例请求体。接下来是 XML 和 JSON 的具体`curl`示例。

```
# HTTP Method and Endpoint
DELETE http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/version/?check=delete

# Example XML Request Body with Explanation
<CellSet>
  <Row key="Base64 Encoded RowKey">
    <Cell column="Base64 column family : qualifer">Base64 check value</Cell>
  </Row>
</CellSet> 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:text/xml**

```
curl -i -H 'Accept: text/xml' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/version/?check=delete' \
  -d '
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <CellSet>
    <Row key="cm93a2V5">
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">Y2hlY2t2YWx1ZQ==</Cell>
    </Row>
  </CellSet>' 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:应用/json**

```
curl -i -H 'Accept: application/json' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/version/?check=delete' \
  -d '
  {
    "Row": [
      {
        "key": "cm93a2V5",
        "Cell": [
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "Y2hlY2t2YWx1ZQ=="}
        ]
      }
    ]
  }' 
```

Enter fullscreen mode Exit fullscreen mode

### 检查并删除所有版本的限定符

这个`checkAndDelete`调用将检查请求体中指定的特定限定符值，并删除 URL 中指定的限定符的所有版本。下面是 HTTP 方法和端点，后面是一个带有解释的示例请求体。接下来是 XML 和 JSON 的具体`curl`示例。

```
# HTTP Method and Endpoint
DELETE http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/?check=delete

# Example XML Request Body with Explanation
<CellSet>
  <Row key="Base64 Encoded RowKey">
    <Cell column="Base64 column family : qualifer">Base64 check value</Cell>
  </Row>
</CellSet> 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:text/xml**

```
curl -i -H 'Accept: text/xml' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/?check=delete' \
  -d '
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <CellSet>
    <Row key="cm93a2V5">
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">Y2hlY2t2YWx1ZQ==</Cell>
    </Row>
  </CellSet>' 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:应用/json**

```
curl -i -H 'Accept: application/json' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily:qualifier/?check=delete' \
  -d '
  {
    "Row": [
      {
        "key": "cm93a2V5", 
        "Cell": [
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "Y2hlY2t2YWx1ZQ=="}
        ]
      }
    ]
  }' 
```

Enter fullscreen mode Exit fullscreen mode

### 检查并删除列族

这个`checkAndDelete`调用将检查请求体中指定的特定限定符值，并删除 URL 中指定的列族。下面是 HTTP 方法和端点，后面是一个带有解释的示例请求体。接下来是 XML 和 JSON 的具体`curl`示例。

```
# HTTP Method and Endpoint
DELETE http://localhost:8084/namespace:table/rowkey/columnfamily/?check=delete

# Example XML Request Body with Explanation
<CellSet>
  <Row key="Base64 Encoded RowKey">
    <Cell column="Base64 column family : qualifer">Base64 check value</Cell>
  </Row>
</CellSet> 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:text/xml**

```
curl -i -H 'Accept: text/xml' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily/?check=delete' \
  -d '
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <CellSet>
    <Row key="cm93a2V5">
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">Y2hlY2t2YWx1ZQ==</Cell>
    </Row>
  </CellSet>' 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:应用/json**

```
curl -i -H 'Accept: application/json' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/columnfamily/?check=delete' \
  -d '
  {
    "Row": [
      {
        "key": "cm93a2V5",
        "Cell": [
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "Y2hlY2t2YWx1ZQ=="}
        ]
      }
    ]
  }' 
```

Enter fullscreen mode Exit fullscreen mode

### 检查并删除行

这个`checkAndDelete`调用将检查请求体中指定的特定限定符值，并删除 URL 中指定的行。下面是 HTTP 方法和端点，后面是一个带有解释的示例请求体。接下来是 XML 和 JSON 的具体`curl`示例。

```
# HTTP Method and Endpoint
DELETE http://localhost:8084/namespace:table/rowkey/?check=delete

# Example XML Request Body with Explanation
<CellSet>
  <Row key="Base64 Encoded RowKey">
    <Cell column="Base64 column family : qualifer">Base64 check value</Cell>
  </Row>
</CellSet> 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:text/xml**

```
curl -i -H 'Accept: text/xml' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/?check=delete' \
  -d '
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <CellSet>
    <Row key="cm93a2V5">
      <Cell column="Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==">Y2hlY2t2YWx1ZQ==</Cell>
    </Row>
  </CellSet>' 
```

Enter fullscreen mode Exit fullscreen mode

**内容类型:应用/json**

```
curl -i -H 'Accept: application/json' \
  -XDELETE 'http://localhost:8084/namespace:table/rowkey/?check=delete' \
  -d '
  {
    "Row": [
      {
        "key": "cm93a2V5",
        "Cell": [
          {"column": "Y29sdW1uZmFtaWx5OnF1YWxpZmllcg==", "$": "Y2hlY2t2YWx1ZQ=="}
        ]
      }
    ]
  }' 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

如上所述，我的团队将为 [HBASE-7129](https://issues.apache.org/jira/browse/HBASE-7129) 制作一个文档补丁来改进 HBase REST 服务器文档。除了`checkAndPut`和`checkAndDelete`，其余 API 支持`CheckAndMutate`、`IncrementColumnValue`和`AppendValue`。我们使用的 HBase 版本不支持这三个端点，也没有记录。如果我们升级，我们可以看看他们。我们希望其他人也能从这个功能中受益，因为原子操作支持已经在 REST API 中存在 5 年多了。