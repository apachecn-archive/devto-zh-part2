# 使用 _writev()为 Elasticsearch 创建一个快速、可写的流

> 原文：<https://dev.to/youknowfordevs/using-writev-to-create-a-fast-writable-stream-for-elasticsearch-2ghb>

我们都知道节点流有多棒。但是直到我最近需要为 Elasticsearch 创建(又一个)可写的流包装器时，我才意识到流 API 能为你做多少工作。尤其是`_writev()`方法有多强大。

我希望将 Elasticsearch 客户端封装在一个可写的流中，这样我就可以在流管道中使用它。我以前在许多不同的背景下做过很多次，比如创建与 Gulp 和 Vinyl 一起使用的 Elasticsearch 模块，所以我准备好遵循通常的模式:

*   我的第一步是使用 Elasticsearch API 建立一个 Elasticsearch 客户端；
*   然后我会添加一个函数，该函数会调用应该写入 Elasticsearch 服务器的任何条目；
*   为了加快写入速度，我不会将这个条目直接写入服务器，而是在一个数组中缓冲每个条目(数组的大小当然是可配置的)。然后，一旦缓冲区满了，就会使用[批量更新 API](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-bulk) 将条目一起写入 Elasticsearch 服务器(这比一次写入一条记录要快得多)；
*   当可写流的数据源指示没有更多的数据要发送时，我会检查缓冲区中是否还有数据，如果有，就调用一个‘flush’函数；
*   一旦所有数据都被清空，我就会删除客户端。

这些可能都不会让你感到惊讶，毫无疑问，你自己也会以同样的方式编写一个 Elasticsearch 的界面。

但是可能让你吃惊的是——特别是如果你已经有一段时间没有查看过 [Node 的可写流](https://nodejs.org/api/stream.html#stream_writable_streams)——这些步骤中有多少可以由节点库为你完成。

首先，让我们创建一个扩展[节点流`Writable`类](https://nodejs.org/api/stream.html#stream_class_stream_writable) :
的类

```
const stream = require('stream')

class ElasticsearchWritableStream extends stream.Writable {
}

module.exports = ElasticsearchWritableStream 
```

现在我们可以开始添加列表中的每个特性。

# 创建弹性搜索客户端

我们上面描述的第一步是使用 [Elasticsearch API](https://www.npmjs.com/package/elasticsearch) 创建一个 Elasticsearch 客户端，所以让我们把它添加到我们的类的构造函数:

```
const stream = require('stream')
const elasticsearch = require('elasticsearch')

class ElasticsearchWritableStream extends stream.Writable {
  constructor(config) {
    super()
    this.config = config

    /**
     * Create the Elasticsearch client:
     */

    this.client = new elasticsearch.Client({
      host: this.config.host
    })
  }
}

module.exports = ElasticsearchWritableStream 
```

我们现在可以通过一些配置来调用我们的类，并且我们将拥有一个带有 Elasticsearch 客户端的可写流:

```
const sink = new ElasticsearchWriteStream({ host: 'es:9200' }) 
```

当然，这个流还没有做任何事情，所以让我们添加一个方法，每当其他流想要写记录时，流基础设施都会调用这个方法。

# 书写记录

当实现一个可写的流类时，我们需要提供的唯一方法是 [`_write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback_1) ，每当提供该数据的流中有新数据时，就会调用该方法。在我们的 Elasticsearch 流的例子中，要在上转发记录，我们只需要在客户端调用我们在构造函数
中创建的 [`index()`](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-index)

```
class ElasticsearchWritableStream extends stream.Writable {
  constructor(config) {
    ...
  }

  /**
   * When writing a single record, we use the index() method of
   * the ES API:
   */

  async _write(body, enc, next) {

    /**
     * Push the object to ES and indicate that we are ready for the next one.
     * Be sure to propagate any errors:
     */

    try {
      await this.client.index({
        index: this.config.index,
        type: this.config.type,
        body
      })
      next()
    } catch(err) {
      next(err)
    }
  }
} 
```

请注意，一旦我们成功地写入了记录，我们就调用`next()`向流媒体基础设施表明我们很高兴收到更多的记录，即更多对`_write()`的调用。事实上，如果我们*不*呼叫`next()`，我们就不会收到更多的数据。

# 指标和类型

当写入 Elasticsearch 时，我们需要提供文档的索引名称和类型，所以我们已经将它们添加到提供给构造函数的配置中，然后我们可以将这些值传递给对`index()`的调用。我们现在需要像这样调用我们的流:

```
const sink = new ElasticsearchWriteStream({
  host: 'es:9200',
  index: 'my-index',
  type: 'my-type'
}) 
```

# 缓冲

就目前情况来看，我们已经有了一个可写的工作流用于 Elasticsearch。然而，如果我们计划插入几十万条记录，那么它会很慢，一个简单的优化是缓冲记录并使用[批量更新 API](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-bulk) 。

## 批量更新 API

批量更新 API 允许我们同时执行许多操作，可能一次插入数千条记录。我们需要创建一个包含条目对的列表[，而不是像调用`index()`那样定义要插入的每条记录；一个指示要执行的操作(如插入或更新),另一个包含操作的数据。](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/docs-bulk.html)

## 使用数组

这里通常的“go to”实现是在类构造函数中创建一个数组，然后在每次调用`_write()`时将数据行推入该数组。然后，当数组已满时，仍然在`_write()`方法中构造对批量 API 的调用。

然而这里的问题是，为了正确地实现背压，我们需要与`next()`函数进行相当复杂的交互；只要缓冲区未满，我们就需要允许数据流向我们的流，并且我们需要防止新数据到达，直到我们有机会将记录写入 Elasticsearch。

原来，节点流 API 可以为我们管理缓冲区*和背压*。

## _writev()

虽然我们需要在可写流类中提供的最少的方法是一个`_write()`方法，但是如果我们愿意，我们还可以创建另一个方法，称为 [`_writev()`](https://nodejs.org/api/stream.html#stream_writable_writev_chunks_callback) 。每个记录调用一次第一个函数，第二个函数用记录的*列表*调用。从某种意义上来说，流式 API 完成了整个过程*创建一个数组并存储项目，直到数组满了，然后在*位上为我们发送它们。

下面是我们的`_writev()`方法的样子:

```
class ElasticsearchWritableStream extends stream.Writable {
  ...

  async _writev(chunks, next) {
    const body = chunks
    .map(chunk => chunk.chunk)
    .reduce((arr, obj) => {
      /**
       * Each entry to the bulk API comprises an instruction (like 'index'
       * or 'delete') on one line, and then some data on the next line:
       */

      arr.push({ index: { } })
      arr.push(obj)
      return arr
    }, [])

    /**
     * Push the array of actions to ES and indicate that we are ready
     * for more data. Be sure to propagate any errors:
     */

    try {
      await this.client.bulk({
        index: this.config.index,
        type: this.config.type,
        body
      })
      next()
    } catch(err) {
      next(err)
    }
  }
} 
```

流式 API 将缓冲记录，然后在某个时候将它们全部交给我们的`_writev()`函数。这给了我们缓冲数据的主要好处——我们可以使用批量更新 API——而实际上不必创建和管理缓冲区，也不必考虑背压。

# 缓冲区大小

如果我们自己创建缓冲区，我们就可以完全控制缓冲区的大小，但是如果节点流 API 为我们管理缓冲区，我们还能控制缓冲区的大小吗？

事实证明我们可以，通过使用[通用`highWaterMark`特性](https://nodejs.org/api/stream.html#stream_constructor_new_stream_writable_options)，该特性在整个 streams API 中用于指示缓冲区应该有多大。

在我们的可写流中实现这一点的最佳方式是为我们的构造函数设置两个参数:

*   一个将为 Elasticsearch 连接提供配置，如服务器地址、超时配置、索引名称和类型等；
*   另一个为可写流本身提供设置，比如`highWaterMark`。

这很容易添加，就像这样:

```
class ElasticsearchWritableStream extends stream.Writable {
  constructor(config, options) {
    super(options)
    this.config = config

    /**
     * Create the Elasticsearch client:
     */

    this.client = new elasticsearch.Client({
      host: this.config.host
    })
  }

  ...
} 
```

现在，我们可以通过设置构造函数中的选项来控制缓冲区的大小——从而控制每次调用批量 API 时写入的记录数量:

```
const esConfig = {
  host: 'es:9200',
  index: 'my-index',
  type: 'my-type'
}
const sink = new ElasticsearchWriteStream(
  esConfig,
  { highWatermark: 1000 }
) 
```

# 关闭 Elasticsearch 客户端

我们最初的清单剩下的就是当没有更多数据要接收时关闭客户机。要实现这一点，我们需要做的就是添加另一个可选方法， [`_destroy()`](https://nodejs.org/api/stream.html#stream_writable_destroy_err_callback) 。当没有更多的数据时，这由流基础结构调用，看起来像这样:

```
_destroy() {
  return this.client.close()
} 
```

# 结论

如您所见，节点流 API 已经为我们做了大量的缓冲工作，这意味着我们不会因为试图正确实现背压而陷入困境。通过为我们提供方法`_write()`、`_writev()`和`_destroy()`，我们的代码变得非常简洁，并且只关注加速和破坏与 Elasticsearch 的连接所需的部分，以及写入单个记录或一批记录所需的函数。完整的实现如下所示:

```
const stream = require('stream')
const elasticsearch = require('elasticsearch')

class ElasticsearchWritableStream extends stream.Writable {
  constructor(config, options) {
    super(options)
    this.config = config

    /**
     * Create the Elasticsearch client:
     */

    this.client = new elasticsearch.Client({
      host: this.config.host
    })
  }

  _destroy() {
    return this.client.close()
  }

  /**
   * When writing a single record, we use the index() method of
   * the ES API:
   */

  async _write(body, enc, next) {

    /**
     * Push the object to ES and indicate that we are ready for the next one.
     * Be sure to propagate any errors:
     */

    try {
      await this.client.index({
        index: this.config.index,
        type: this.config.type,
        body
      })
      next()
    } catch(err) {
      next(err)
    }
  }

  async _writev(chunks, next) {
    const body = chunks
    .map(chunk => chunk.chunk)
    .reduce((arr, obj) => {
      /**
       * Each entry to the bulk API comprises an instruction (like 'index'
       * or 'delete') and some data:
       */

      arr.push({ index: { } })
      arr.push(obj)
      return arr
    }, [])

    /**
     * Push the array of actions to ES and indicate that we are ready
     * for more data. Be sure to propagate any errors:
     */

    try {
      await this.client.bulk({
        index: this.config.index,
        type: this.config.type,
        body
      })
      next()
    } catch(err) {
      next(err)
    }
  }
}

module.exports = ElasticsearchWritableStream 
```