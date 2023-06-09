# 星期五爆炸#53

> 原文：<https://dev.to/horia141/friday-blast-53-2hke>

[关于结构并发或 go 语句被认为有害的注释(2018)](https://vorpus.org/blog/notes-on-structured-concurrency-or-go-statement-considered-harmful/)——证明`go`语句(或`async/await`或来自其他语言的等效函数)对于并发编程就像`goto`对于 50 年代和 60 年代的早期编程一样好。这可不太好。作者提出的托儿所的想法*看起来不错，尽管没有更好的语法形式的语言支持，我真的看不出它会流行起来。*

[使用 Redis 集群扩展高流量速率限制堆栈(2018)](https://brandur.org/redis-cluster) -研究 Stripe 如何改进其速率限制处理。可怕的是，*一台 Redis 机器做了所有这些事情*(当然，还有一堆二级)。关于“环境”错误率的讨论也很棒——这是小规模系统和大规模系统的一大区别。

[网络的建构(2018)](https://frontendian.co/the-tectonics-of-the-web)-IETF、W3C 和 ECMA 的概述-这三个标准组织或多或少地在更大程度上定义了网络和互联网上发生的事情。

[我想要的 Web(2018)](https://dev.to/quii/the-web-i-want-43o)——少一些 JS，多一些 HTML & CSS。当情况如此时。我认为“想想所有互联网不好的地方”/“电池的事情”的论点是最好的动摇人们保持事情简单。

[利用服务器发送的事件开发实时 web 应用(2018)](https://auth0.com/blog/developing-real-time-web-applications-with-server-sent-events/) - SSEs 是一种供人们使用的新 API。它们是 web 套接字允许的双向通信的专门化。只有服务器被允许发送数据，以响应客户端请求。所以它们是标准的常规“请求-响应”方法的对偶。这篇文章深入探讨了它们和它们的优势案例。尤其是那些失去联系的人。