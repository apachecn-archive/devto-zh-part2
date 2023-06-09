# 如何用 Argon2 做自己

> 原文：<https://dev.to/justyntemme/how-to-dos-yourself-with-argon2-20a4>

最近，在对一个客户的服务器进行渗透测试时，我发现了一个通过简单的 post 请求进行的 DOS 攻击。在一个接受文本输入和散列方法并返回散列字符串的页面上，我能够 DOS 整个服务器。

## 饿死 CPU

这个漏洞是一个简单的 bash 脚本，它发出一个 POST 请求，文本为`testing`，散列函数为`Argon2`。现在需要注意的一件有趣的事情是，每个哈希函数占用了多少 CPU。我同意，许多人会认为这是使用 Argon2 而不是 MD5 这样的弱散列函数的更多理由。问题不在 Argon2 内部。这是一个更好的散列函数。然而，当一个小的 1 核 512mb 数字 ocean droplet 被告知一个接一个地散列字符串数千次时，CPU 使用率很快从空闲变为 100%。这导致整个服务器停止响应，直到我们停止脚本。

## 缓解措施

我们怎样才能阻止这样的攻击发生呢？和所有的安全措施一样，在设计软件时必须考虑到这一点。必须将整个系统视为一个由许多部分组成的单一功能体。

### 效率 O(o)

当设计任何一个软件时，从 API 到全面的应用程序，人们必须考虑整个系统的效率。请求的大小是否与处理量相匹配？很多人会认为这是 O(o)，或者`amount of time grows linearly with the size of the input`。你可以在这里找到引用的解释和更多关于大 O 符号的有条不紊的解释。

### 限速

如果由于软件的需求，效率的目标不能被简单地满足，那么可以考虑限制请求的速率，或者称为速率限制。通过阻止攻击者利用低效的请求，您可以让服务器有足够的时间来完成功能并返回进行下一个操作，而不是慢慢死去。

#### 在 Go 中对 API 进行速率限制

众所周知，Go 是我最喜欢的编程语言。我再次解释了使用语言内置的工具创建安全有效的应用程序是多么简单。正如大多数事情所预期的那样，goes 速率限制的实现足够基础，任何初级开发人员都可以使用，并且可以通过工具进行扩展，这将使任何高级开发人员欣喜若狂。

`import "time"`
我们从导入时间包开始。这将允许我们指定允许请求的频率。

```
rate:= time.Second / 10

burstLimit := 100

tick := time.NewTicker(rate)

defer tick.Stop() 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们定义了限制器的选项，并实现了突发限制以允许快速响应调用，同时还限制了给定时间范围内的数量。

这将创建一个通道，另一个函数可以窥视并查看时间或突发限制。这将允许我们使用 go 例程来管理请求。

```
go func() {

  for t := range tick.C {

    select {

      case throttle <- t:

      default:

    }

  }  // does not exit after tick.Stop()

}() 
```

Enter fullscreen mode Exit fullscreen mode

这是将计时器向前计时的 goRoutine，在每次计时时，检查请求是否大于 burstLimit。

```
for req := range requests {

  <-throttle  // rate limit our Service.Method RPCs

  go client.Call("Service.Method", req, ...)

} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的最后一段代码。这将检查请求并检查 goRoutine 使用的通道内的计数器。如果打勾。未调用 Stop，则服务请求将照常进行。要查看完整的源代码，以及更高级的速率限制实现，请访问 Golang Wiki

你可以在这里阅读更多我的作品