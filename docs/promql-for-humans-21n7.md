# 面向人类的 PromQL

> 原文：<https://dev.to/timber/promql-for-humans-21n7>

PromQL 是为 Prometheus 设计的内置查询语言。在 Timber [我们发现普罗米修斯很棒](https://timber.io/blog/prometheus-the-good-the-bad-and-the-ugly/)，但是 PromQL 很难让我们理解。这是我们改变现状的尝试。

[![PromQL Cheatsheet](../Images/bda5635a9a00ba7cbbee96e4842e8cb8.png)T2】](https://files.timber.io/pdfs/PromQL+Cheatsheet.pdf)

# 基础知识

## 瞬间向量

*只能绘制瞬时矢量。*

`http_requests_total`

[![http requests total](../Images/a2a4c16dc5fabf52f3dc001483c781cf.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/47m73p4320Qm4Cs6AGOC8A/0afdd1172d332697d4a0563d7ef6dce5/http_requests_total.png)

这给了我们所有的 http 请求，但是我们有两个问题。

1.  数据点太多了，无法破译到底发生了什么。
2.  你会注意到`http_requests_total`只上升，因为它是一个[计数器](https://prometheus.io/docs/concepts/metric_types/#counter)。这些在《普罗米修斯》中很常见，但对图表没有用。

我将向您展示如何实现这两者。

### 按标签筛选很简单。

`http_requests_total{job="prometheus", code="200"}`

[![filter-by-label](../Images/65a77777e8235609c2b8acb76b0af66a.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/1YYMQf9uS4Oi0Q2KeiSWc6/aa1493fe8cef2e23d4430b84c9f9feb4/filter-by-label.png)

### 可以使用正则表达式匹配来检查子串。

`http_requests_total{status_code=~"2.*"}`

[![substring](../Images/2f526fd34ac3420492d98e8dd95b0c21.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/49euNSlsFGU4kA0qa44oEG/0538688f02c42cafd31054922ffd8b10/substring.png)

如果你有兴趣了解更多，这里的是 Regex 上的文档。

## 距离矢量

*包含及时返回的数据。*

回想一下:只有即时向量可以被绘制。您将很快能够看到如何使用函数来可视化范围向量。

`http_requests_total[5m]`

也可以用(s，m，h，d，w，y)来表示(秒，分，小时，...)分别。

# 重要功能

## 为距离矢量

你会注意到我们能够绘制所有这些函数。因为只有即时向量可以被图形化，所以它们接受一个范围向量作为参数并返回一个即时向量。

### 在过去 5 分钟内平均增加`http_requests_total`。

`rate(http_requests_total[5m])`

[![rate](../Images/0405843ec5db70a5b5069a042769f15a.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/1oQ9xOzINe4mIqIwkIwcu8/a421fa29abb27648e967a38e8425ef65/rate.png)

### 气呼呼的

查看 2 个最近的样本(过去 5 分钟内)，而不是像`rate`那样取平均值

`irate(http_requests_total[5m])`

[![irate](../Images/857ba640efeca84e533ed2c5c070a2e6.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/3MCqVRoOPCOuKG6MOsqYcu/0aac574e965b4d960bf83ad8477ce5f9/irate.png)

发出警报时最好使用`rate`，因为它会创建一个平滑的图形，因为数据是一段时间内的平均值。*由于重复触发阈值，尖峰图会导致警报过载、疲劳和所有人的不良时间。*

### 最近一小时的 HTTP 请求。

这等于`rate` * #秒

`increase(http_requests_total[1h])`

[![increase](../Images/4192c609b0447d267c7ff4c6dc881237.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/4UqNZH5G80WaowamI884MY/9e991a116fd1606b42c9978b72edb26f/increase.png)

这些只是函数的一小部分，只是我们发现最流行的。其余的[你可以在这里](https://prometheus.io/docs/prometheus/latest/querying/functions/)找到。

## 为瞬间向量

### 被状态码打断

`sum(rate(http_requests_total[5m]))`

你会注意到上面的`rate(http_requests_total[5m])`提供了大量的数据。您可以使用您的标签过滤这些数据，但是您也可以使用`sum`将您的系统作为一个整体来看(或者两者都做)。

同样也可以用`min`、`max`、`avg`、`count`、`quantile`。

[![sum-rate](../Images/1065a4510e38ab66768804ab8c4f3623.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/6t6cQFBJ7i6yqOU2CCsQwW/4216d8b6efef36ec0b49ae601a65e545/sum-rate.png)

这个查询告诉您总共有多少个 HTTP 请求，但是对破解系统中的问题没有直接的帮助。我将向您展示一些允许您深入了解您的系统的功能。

### 按状态码求和

`sum by (status_code) (rate(http_requests_total[5m]))`

您也可以使用`without`而不是`by`对所有没有作为参数传递给 without 的内容求和。

[![sum-by-rate](../Images/57684e7450dd80e59623007c2b1829a3.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/MyLjzpWl4kcm0gwGSSaOA/4473db77e505c9322f68b533c2a5cc86/sum-by-rate.png)

现在，您可以看到每个状态代码之间的区别。

## 偏移

您可以使用`offset`来更改瞬时向量和距离向量的时间。这有助于在确定警报条件时比较当前使用情况和过去使用情况。

`sum(rate(http_requests_total[5m] offset 5m))`

记得把`offset`直接放在选择器后面。

# 运算符

运算符可以用于标量、向量或二者的混合。向量之间的运算期望为每一边找到匹配元素(也称为一对一匹配)，除非另有说明。

还有算术(+，-，*，/，%，^)，比较(==，！=、>、 =、< =)和逻辑(与、或、除非)运算符。

## 矢量匹配

**一对一**

向量是相等的，标签也是相等的。

### API 5xx 是 HTTP 请求的 10%

`rate(http_requests_total{status_code=~"5.*"}[5m]) > .1 * rate(http_requests_total[5m])`

[![api5xx](../Images/7215aa5c814e167e016e69b814905fac.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/22x7QobKuMEIU88AYkea0Y/ded5098f90c33fb9e9cf7b978580de3f/api5xx.png)

每当一个实例的 HTTP 请求中有超过 10%是错误时，我们就会用图表表示出来。在比较速率之前，PromQL 首先检查以确保向量的标签是相等的。

您可以使用`on`来比较某些标签，或者使用`ignoring`来比较除。

## 多对一

可以使用比较和算术运算，其中一边的一个元素可以与另一边的许多元素匹配。你必须明确告诉普罗米修斯如何处理额外的维度。

如果左边有更高的基数，可以使用`group_left`，否则使用`group_right`。

# 例子

*免责声明*:出于隐私原因，我们使用`Legend Format`隐藏了图片中的一些信息。

### 按实例划分的 CPU 使用率

`100 * (1 - avg by(instance)(irate(node_cpu{mode='idle'}[5m])))`

5 分钟窗口内每个实例的平均 CPU 使用率。

[![cpu](../Images/bd5cecbc859e3f009d6cfac674552c04.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/3rbFceQUaQ0w2O2sogi4w6/bbf45cfd8a92ac6a7fcfe24eca268b6a/cpu.png)

### 内存使用情况

`node_memory_Active / on (instance) node_memory_MemTotal`

实例使用的内存百分比。

[![memory](../Images/d6e464876804e61f7cc3757daa20bd60.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/5wKZrzMiHu6y80au0AOQ2G/3a7419d79587b9e8690787b1099a66d0/memory.png)

### 磁盘空间

`node_filesystem_avail{fstype!~"tmpfs|fuse.lxcfs|squashfs"} / node_filesystem_size{fstype!~"tmpfs|fuse.lxcfs|squashfs"}`

实例使用的磁盘空间百分比。我们正在寻找可用空间，忽略在它们的`fstype`中有`tmpfs`、`fuse.lxcfs`或`squashfs`的实例，并将其除以它们的总大小。

[![disk](../Images/d8bca0fbce36ed0a4911369d5a5f2874.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/66tln8JKuWuyQ2CwA4gaCY/40de7b35900834ac2de4c47757db8d63/disk.png)

### HTTP 错误率占流量的百分比

`rate(http_requests_total{status_code=~"5.*"}[5m]) / rate(http_requests_total[5m])`

[![http error rates](../Images/c397f8f607588088d256967ed53cadad.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/11WaHWtONckUeYcqY4YYyc/ba85a9ba85c37d2b74d81ff60d6f4dff/http_error_rates.png)

### 过去 24 小时内触发的警报

`sum(sort_desc(sum_over_time(ALERTS{alertstate="firing"}[24h]))) by (alertname)`

[![alerts firing](../Images/0742b1f7555e8b09f318185e320e0482.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/bk7qkd88FykSwoI8gIeoa/1b865a815087b373da64d13483fc3d23/alerts_firing.png)

你可以在这里找到更多有用的例子[。](https://github.com/infinityworks/prometheus-example-queries)

# 可观察性的三大支柱

在观察您的应用程序时，理解度量标准在哪里是很重要的。我建议你看一看[可观察性的 3 个支柱](https://peter.bourgon.org/blog/2017/02/21/metrics-tracing-and-logging.html)原则。度量是你的可观察性栈的重要部分，但是*日志*和跟踪同样如此。

我们是 Timber 的一家基于云的日志记录公司，可以根据上下文无缝增强您的日志。我们已经有了[一款很棒的产品](https://timber.io/)，你可以免费试用！

[![](../Images/aa5c715399ff218855ddd92755157269.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---Pfx3BIg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/h6vh38q7qvzk/5BUP5dDcrKae4yyaoy8ocE/ba33ae45edec6325109f05a44407a2e2/footer.png)