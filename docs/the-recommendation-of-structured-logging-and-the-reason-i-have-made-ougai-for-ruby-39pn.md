# 结构化日志的推荐以及我为 Ruby 做 Ougai 的原因

> 原文：<https://dev.to/tilfin/the-recommendation-of-structured-logging-and-the-reason-i-have-made-ougai-for-ruby-39pn>

## 结构化日志记录

**结构化测井**就是做一个易于机械处理的测井。

## 普通日志和结构化日志的比较

通常的日志基本上只是*时间戳*、*级别*和*消息串*。作为要作为日志保存的事件(事件)的上下文的信息被适当地嵌入消息中。人们很容易在控制台上阅读。

在结构化日志中，成为嵌入在消息中的上下文的信息被独立地给予日志结构的字段。所以后面分析起来比较容易。输出日志通常是基于文本的 JSON。

我们来比较一下这个。普通 Ruby Logger 和我自己 Ougai 的日志会如下。这是一个日志“用户创建的文章”(文章剃)。

```
logger.info "User created article  (user_id=#{user.id} article_id=#{article.id}" 
```

Enter fullscreen mode Exit fullscreen mode

```
I, [2018-05-13T17:51:08.772542 #6253]  INFO -- : User created article  (user_id=123 article_id=45) 
```

Enter fullscreen mode Exit fullscreen mode

```
logger.info "User created article", user_id: user.id, article_id: article.id 
```

Enter fullscreen mode Exit fullscreen mode

```
{"pid":6253,"level":20,"time":"2018-05-13T17:52:25.147+09:00","msg":"User created article","user_id":123,"article_id":45}" 
```

Enter fullscreen mode Exit fullscreen mode

※它不同于默认格式

如您所见，您必须嵌入正常日志并将附带信息转换成字符串。另一方面，由于 JSON 效应，结构化日志较长，因此很难在控制台中读取。但是，如果您在格式化程序或解析机制的操作环境中使用带有切换功能的日志查看器，这就不是问题。

结构化日志更容易分析的一点是，例如，如果您想只提取某些用户的日志，简单地使用`grep "user_id=10"`将会导致挂钩那些 user_id 类似于普通日志中的`101`的条目。另一方面，结构化日志(主要使用 JSONPath)可以通过类似`$.user_id = 10`的工具轻松可靠地过滤。

## 日志管理服务和结构化日志

用于日志管理和分析的 SaaS，如谷歌云上的 [AWS CloudWatch Logs](https://aws.amazon.com/jp/cloudwatch/) 、 [Stackdriver Logging](https://cloud.google.com/logging/) 和 [Loggly](https://www.loggly.com/) 、 [Logentries](https://logentries.com/) 都支持结构化日志，否则你无法使用这些服务。但是由于每个服务提供的库本身并不对应结构化的输出，所以我认为最好是 Fluentd 中继，最好是现状发送。

## 制作欧盖的原因

最初，我在 Node.js 中编写前端 API 服务器，并经常参与用 Ruby / Rails 创建后端管理服务的项目。Node.js 有一个著名的 JSON 日志记录器，名叫[班扬](https://github.com/trentm/node-bunyan)，我很喜欢它。但是 Ruby 有处理几个结构化日志的库，但是每个库都是作为一个框架。Node.js 本身有类似`console.info`的控制台输出函数，但是 Ruby 有自己的日志记录器。在另一个微服务中，每个服务通常用多种语言实现，但如果可能的话，我希望统一格式，以便对日志进行跨部门分析。

有了这些背景，我基于 Ruby 的原始 logger 构建了自己的 logger，这是一个具有上述 Bunyan 兼容日志输出格式的 Logger。那是欧盖。(我是在班扬从英国文学界起名之后，从一位日本文学界人士那里起名 Ougai 的。)

GitHub:til fin/Ougai——Ruby 结构化日志能够轻松处理消息、自定义数据或异常，并生成 JSON 或人类可读的日志。

由于它是 Ruby 原始 Logger 类的扩展，所以即使它是突然引入的，它也不会仅仅因为出现在消息字段中就崩溃。你可以逐渐将上下文转移到一个独立的领域。格式是标准的，对于 JSON 输出，它是与 Node.js 的 Bunyan 兼容的 [Pino](https://github.com/pinojs/pino) ，**可读的**，它是彩色的，易于在终端使用 [awesome_print](https://github.com/awesome-print/awesome_print) 读取。当然，对于 JSON，您可以使用 Bunyan 和 Pino 分别拥有的专用日志查看器命令进行浏览。还有一个功能可以让普通信息很容易通过 hook 放进去。

[https://github . com/tilfin/ougai/wiki](https://github.com/tilfin/ougai/wiki)

wiki 还包含了与 Rails、Sidekiq、Fluentd 等结合使用的设置示例。

[https://rubygems.org/gems/ougai](https://rubygems.org/gems/ougai)