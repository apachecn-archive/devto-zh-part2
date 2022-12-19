# Go:使用 pprof 调试内存泄漏

> 原文：<https://dev.to/davidsbond/golang-debugging-memory-leaks-using-pprof-5di8>

## 简介

我在 OVO 能源公司担任软件工程师，我的团队正在实施客户服务部使用的 CRM 解决方案。我们目前正在构建一套新的微服务来取代现有的服务。我们的一个微服务负责将数据从旧系统迁移到新系统。

在部署新版本的服务几天后，我打开相关的监控仪表板，看到了以下内容:

[![Memory usage graph](../Images/3f107a49c1772a48b4eda542428adf45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sHbqTXQf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidsbond.github.io/assets/2018-08-08-debugging-memory-leaks-using-pprof/1.png)

根据这个图表，我们在某个地方有内存泄漏。这很可能是由于服务中 goroutines 的管理问题。然而，该服务严重依赖于并发性，因此找到漏洞可能不那么容易。幸运的是，goroutines 是轻量级的，在泄漏成为真正的/昂贵的问题之前，允许有合理的时间来找出泄漏在哪里。12pm 标记上的两个尖峰是迁移发生的时间。

### 背景

在几周的时间里，我设计并实现了这项服务，并将其托管在我们位于 [GCP](https://cloud.google.com/) 的 [Kubernetes](https://kubernetes.io) 集群中，确保我添加了监控功能，以便为生产做好准备。这包括使用 [Stackdriver](https://cloud.google.com/stackdriver/) 进行健康检查、基于日志的指标和正常运行时间检查的 HTTP 端点。

该服务必须与一些外部依赖项进行通信，这些依赖项是:

*   Apache Kafka - Kafka 允许服务发布和订阅记录流，类似于消息队列或企业消息系统。另一个服务发布了一个事件，表示客户已经准备好接受我们的迁移。
*   融合模式注册中心(Confluent Schema Registry)——注册中心允许我们将版本化的模式应用于 Kafka 事件，并用于将来自 [Apache Avro](https://avro.apache.org/) 格式的消息解码为 JSON 格式。
*   [PostgreSQL](https://www.postgresql.org/) -用于存储客户迁移信息的关系数据库(创建的记录、任何错误和警告等)。
*   两个 [Salesforce](https://www.salesforce.com/) 实例——这些是客户支持人员日常工作的地方。一个包含 V1 数据源，另一个存储新的 V2 数据。

从所有这些依赖关系中，我们得到了一个类似于这样的健康检查:

```
{
    "status": "UP",
    "uptime": "22h54m27.491102074s",
    "goroutines": 24,
    "version": "go1.9.7",
    "sf1": {
        "status": "UP"
    },
    "sf2": {
        "status": "UP"
    },
    "database": {
        "status": "UP"
    },
    "kafka": {
        "status": "UP"
    },
    "registry": {
        "status": "UP"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我要注意的第一件事是，使用`runtime.NumGoroutine()`查看正在运行的 goroutines 的数量对于识别内存泄漏的来源非常有帮助。我建议在您的生产环境中采用某种方式来监控这一点。在这个场景中，我们的 HTTP 健康检查返回正在运行的 goroutines 的数量。

在泄漏的那天，我看到 goroutines 的数量超过了 100000，并且随着每个健康检查请求的增加而稳步上升。下面是我在调试这个问题时采取的步骤。

### 通过 HTTP 启用 pprof 输出

`pprof`工具将自己描述为“可视化和分析概要数据的工具”，你可以在这里查看它的 GitHub 知识库[。这个工具允许我们获得 Go 程序底层操作的各种度量。出于我们的目的，它允许我们获得运行 goroutines 的详细信息。这里唯一的问题是`pprof`是二进制的。这意味着我们将不得不对生产中的服务运行命令以获得有意义的结果。该应用程序还运行在基于`scratch`图像的](https://github.com/google/pprof) [Docker](https://www.docker.com/) 容器中，这使得使用二进制文件有些侵入性。那么我们怎样才能得到我们需要的分析数据呢？

标准库中的`net/http/pprof`包公开了通过 HTTP 端点提供分析数据的`pprof`方法。这个项目使用 [mux](https://github.com/gorilla/mux) 作为它的 url 路由器，所以可以使用`HandleFunc`和`Handle`方法
来公开端点

```
// Create a new router
router := mux.NewRouter()

// Register pprof handlers
router.HandleFunc("/debug/pprof/", pprof.Index)
router.HandleFunc("/debug/pprof/cmdline", pprof.Cmdline)
router.HandleFunc("/debug/pprof/profile", pprof.Profile)
router.HandleFunc("/debug/pprof/symbol", pprof.Symbol)

router.Handle("/debug/pprof/goroutine", pprof.Handler("goroutine"))
router.Handle("/debug/pprof/heap", pprof.Handler("heap"))
router.Handle("/debug/pprof/threadcreate", pprof.Handler("threadcreate"))
router.Handle("/debug/pprof/block", pprof.Handler("block")) 
```

Enter fullscreen mode Exit fullscreen mode

一旦我添加了这些处理程序，我就扩展了服务的本地实例并导航到`/debug/pprof/goroutine`端点。

### 了解 pprof 输出

我从`/debug/pprof/goroutine`那里得到的回应相当容易解读，这里有一个例子，展示了卡夫卡消费者的日常行为。

```
goroutine profile: total 25
2 @ 0x434420 0x4344e5 0x404747 0x40451b 0x8a25af 0x8f2486 0x8ee88c 0x461d61
#   0x8a25ae    /vendor/github.com/Shopify/sarama.(*Broker).responseReceiver+0xfe
            /vendor/github.com/Shopify/sarama/broker.go:682
#   0x8f2485    /vendor/github.com/Shopify/sarama.(*Broker).(/vendor/github.com/Shopify/sarama.responseReceiver)-fm+0x35
            /vendor/github.com/Shopify/sarama/broker.go:149
#   0x8ee88b    /vendor/github.com/Shopify/sarama.withRecover+0x4b
            /vendor/github.com/Shopify/sarama/utils.go:45 
```

Enter fullscreen mode Exit fullscreen mode

第一行告诉我们正在运行的 goroutines 的总数。在这个例子中，我运行的服务版本修复了内存泄漏。如你所见，我们总共有 25 个正在运行的 goroutines。下面几行告诉我们有多少例程属于特定的包方法。在这个例子中，我们可以看到`sarama`包中的`Broker`类型的`.responseReceiver`方法正在使用 2 个 goroutines。这是找到泄密罪魁祸首的灵丹妙药。

在这个服务的泄漏版本中，有两条特别突出的线路，它们的活动 goroutines 数量不断增加。

```
14 @ 0x434420 0x444c4e 0x7c87fd 0x461d61
#   0x7c87fc    net/http.(*persistConn).writeLoop+0x15c C:/Go/src/net/http/transport.go:1822

14 @ 0x434420 0x444c4e 0x7c761e 0x461d61
#   0x7c761d    net/http.(*persistConn).readLoop+0xe9d  C:/Go/src/net/http/transport.go:1717 
```

Enter fullscreen mode Exit fullscreen mode

在代码的某个地方，我们创建了一个陷入读/写循环的 HTTP 连接。我决定查看一下标准库的源代码来理解这种行为。我首先查看的是这些例程产生的位置。这是在`net/http/transport.go`文件中，通过`dialConn`方法。其中的全部内容可以在[这里](https://golang.org/src/net/http/transport.go)查看

```
// transport.go:1234

pconn.br = bufio.NewReader(pconn)
pconn.bw = bufio.NewWriter(persistConnWriter{pconn})
go pconn.readLoop() // <- Here is the source of our leak
go pconn.writeLoop()
return pconn, nil 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经确定了我们的泄漏来自哪里，我们需要了解是什么情况导致这些 goroutines 永远不会返回。我注意到 goroutines 的数量只是在健康检查后才增加。在生产系统中，使用 Stackdriver 在不同地区的正常运行时间检查，这种情况大约每分钟发生一次。

经过一点点搜索，我确定泄漏的来源是在我们向汇合模式注册中心请求断言其可用性的过程中。在写这个包的时候，我犯了一些相当幼稚的错误。首先，这里是为注册表创建客户端的`New`方法:

```
func New(baseURL, user, pass string, cache *cache.Cache) Registry {
    return &registry{
        baseURL: baseURL,
        username: user,
        password: pass,
        cache: cache,
        client: &http.Client{},
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

错误一，**总是用合理的值**配置你的 http 客户端。这个问题可以通过包含一个超时来解决一半:

```
func New(baseURL, user, pass string, cache *cache.Cache) Registry {
    return &registry{
        baseURL: baseURL,
        username: user,
        password: pass,
        cache: cache,
        client: &http.Client{
            Timeout: time.Second * 10,
        },
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

做出这一改变后，泄漏的程序在大约 10 秒钟后被清理干净。

虽然这样做是可行的，但是在生成 HTTP 请求的方法中还有一行代码可以解决这个问题。我浏览了一下`http.Request`类型的定义，发现了`Close`标志:

```
// request.go:197

// Close indicates whether to close the connection after
// replying to this request (for servers) or after sending this
// request and reading its response (for clients).
//
// For server requests, the HTTP server handles this automatically
// and this field is not needed by Handlers.
//
// For client requests, setting this field prevents re-use of
// TCP connections between requests to the same hosts, as if
// Transport.DisableKeepAlives were set.
Close bool 
```

Enter fullscreen mode Exit fullscreen mode

我决定检查如果我将这个标志设置为 true 会发生什么，以及它是否会阻止这些 goroutines 的锁定。下面是它在代码中的样子:

```
func (r *registry) buildRequest(method, url string) (*http.Request, error) {
    req, err := http.NewRequest(method, url, nil)

    if err != nil {
        return nil, errors.Annotate(err, "failed to create http request")
    }

    req.SetBasicAuth(r.username, r.password)
    req.Close = true

    return req, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

在实施这些更改并将其部署到生产中后，服务的内存使用量将永远保持在一个健康的水平:

[![Memory usage graph](../Images/ac4d52d6713c0103188dbd835270f53d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5XQKBXRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidsbond.github.io/assets/2018-08-08-debugging-memory-leaks-using-pprof/2.png)

### 吸取教训

*   监控活动 goroutines 的数量，尤其是在依赖并发模式的服务中
*   使用`pprof`为您的服务添加功能以公开分析数据
*   为 HTTP 客户端和请求设置合理的配置值

### 链接

*   [https://www.ovoenergy.com/](https://www.ovoenergy.com/)
*   [https://kubrintes .我](https://kubernetes.io)
*   [https://cloud.google.com/](https://cloud.google.com/)
*   [https://cloud.google.com/stackdriver/](https://cloud.google.com/stackdriver/)
*   [https://kafka.apache.org/](https://kafka.apache.org/)
*   [https://docs . confluent . io/current/schema-registry/docs/index . html](https://docs.confluent.io/current/schema-registry/docs/index.html)
*   [https://avro.apache.org/](https://avro.apache.org/)
*   [https://www.postgresql.org/](https://www.postgresql.org/)
*   [https://www.salesforce.com/](https://www.salesforce.com/)
*   [https://github.com/google/pprof](https://github.com/google/pprof)
*   [https://www.docker.com/](https://www.docker.com/)
*   [https://github.com/gorilla/mux](https://github.com/gorilla/mux)
*   [https://golang.org/src/net/http/transport.go](https://golang.org/src/net/http/transport.go)