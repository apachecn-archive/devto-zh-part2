# HTTP BIN -开发人员工具

> 原文：<https://dev.to/briannipper/http-bin---developer-tools-22eh>

[![](img/818e1debed48461b4bf682710087f197.png)T2】](https://3.bp.blogspot.com/-qdMd20WyI1c/W6mR3wXF1SI/AAAAAAAAHK8/5S2MHzcD9iEBGubIUv1jNKLAkrt8gKqMgCLcBGAs/s1600/10_in_1_screwdriver.jpg)

10 合 1 螺丝刀中我最喜欢的工具之一。吸引力在于，在一个工具中，只需翻转一些部件，你就能找到你想要的东西。在家里的许多小工作中，我可以用这个工具做任何需要的事情。

写软件时，你可以找到类似的有价值的工具，其中一个有价值的工具是[http://httpbin.org](http://httpbin.org/)

像大多数工具一样，简单性可能是欺骗性的，因为拥有一个只是回显您发送的内容的 API 有多难。但是这就是 httpbin 的伟大之处，它完成了，而且非常健壮。

举例来说，当构建前端时，您可能会发现 API 不完整，因此您希望“剔除”调用。有了 httpbin，你可以发送一个调用，这样它就会返回你想要的结果，这样你就连接到了一个真正的 HTTP 调用。

在服务器端，您可能同样需要连接到一个不允许从本地机器调用的服务，但是使用 httpbin，您可以模拟实际的 HTTP 调用。