# 基于 Go 的代理，用于在企业 WiFis 上开发移动网站

> 原文：<https://dev.to/hoverbaum/go-based-proxies-for-developing-mobile-websites-on-corporate-wifis-5gjo>

你可能知道这个场景:

> 我们真的很想在实际的手机上调试网络应用程序，但他们不允许我们公司的 WiFi 设置方式…

<figure>

[![](img/7379e80b76b3a35f1a324c653eba0327.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3xtYq4M---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1d2vwn7kap3i0brauahw.jpeg)

<figcaption>When networks become a show stopped for development.</figcaption>

</figure>

如果你这样做了，请继续关注，因为在这篇博文中，我们将探讨作为开发人员，我们如何处理严密保护的 WiFi 网络，同时还能获得我们需要的所有连接。

我们将从探索一个详细的真实世界场景开始，然后探索一个使用热点和基于 Go 的代理的解决方案。

## 一种示范性的情况

最近，我们正在开发一个基于电容的混合网络应用。要真正调试，您需要在笔记本电脑上运行网络服务器，并通过 WiFi 连接您的手机。唯一的麻烦是:在我们的公司里，出于安全原因，WiFi 电话会被隔离。

> 出于安全原因，电话会被隔离！

幸运的是，通过以太网将笔记本电脑与互联网连接起来，并使用笔记本电脑的 WiFi 打开一个热点，隔离问题就可以很容易地解决。请确保使用强密码来保护它！

遗憾的是，这切断了我们的笔记本电脑与内部网络的连接，我们不得不通过将笔记本电脑连接到公司的 VPN (jup，以太网只能连接到公共互联网)来再次访问内部网络。虽然热点将互联网连接传递给了手机，但它并没有为 VPN 连接带来同样的魔力。那么，我们现在如何将这种联系传递到我们的手机上呢？

我们需要内部网络，因为手机需要连接到部署的测试后端，可以在公司的内部网络上使用，我们的笔记本电脑使用 VPN 连接。

[![](img/5a03bd7bfc091ec57336b4d8ef23a534.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AVNagqLT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7u7zaxhfkhckw5i43zbn.png)

## 代理救援

作为 web 开发人员，我们对在网络上处理流量很熟悉。通常，您需要 NGINX 将来自入口 URL 的请求传递给运行在不同机器上的正确的内部服务(或者对 web 应用程序进行 dockerize)。这就是所谓的代理。

正如刚才提到的，我们可以在我们的笔记本电脑上设置一个 NGINX 或类似的东西，并使用它作为代理。这确实有效。然而，这使得将解决方案签入版本控制变得更加困难。更好的可维护版本是用您选择的语言编写一个简单的代理。我们选择 Go。

```
package main

import (
    "github.com/elazarl/goproxy"
    "log"
    "net/http"
)

func main() {
    proxy := goproxy.NewProxyHttpServer()
    proxy.Verbose = true
    log.Fatal(http.ListenAndServe(":9090", proxy))
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是您现在需要运行的所有代码。运行代理。在您的计算机上启动一个代理。

<figure>

[![](img/12b6af2755b7d3dea2f640da87fee651.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EvBz5E_o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cccgwvbe3oro7kk2fib4.jpeg)

<figcaption>Green light for our traffic thanks to proxies.</figcaption>

</figure>

在你的手机上，你现在需要告诉它**不仅要**连接到你的笔记本电脑热点，还要使用一个代理。为此，你可能需要你的笔记本电脑在热点内的 IP 地址(它可能总是分配给自己同一个)以及端口(这里: *9090* )来设置它。

有了这个，你就可以最终在你的手机上进行开发了。

## 结论

今天我们看了 Go 中的简单代理如何帮助我们在严密保护的企业网络中进行开发。只需几行代码和一点设置，您就可以开始了。

现在，走出去，建立一些真棒✨

* * *

### 倒影

这里有一个明显的问题:*这是个好主意吗？？！？*

我很高兴你这么问。事实上我也不确定。这确实感觉像是一次黑客攻击，事实也确实如此。我们基本上是在绕过一个有充分理由建立的安全措施。我鼓励您将此处的实践视为短期解决方案，并与您的管理层和 IT 部门讨论长期解决方案。

我们非常欢迎任何关于更好解决方案的评论和想法。