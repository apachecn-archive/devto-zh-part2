# 软件中那些你认为理所当然但后来才发现的东西？

> 原文：<https://dev.to/svemaraju/things-in-software-that-you-have-taken-granted-only-to-find-out-later--3ago>

有哪些事情是你一直认为理所当然，后来才发现的？

根据我的经验，我把一些事情归为这一类—

1.  我从来没有考虑过服务器时间偏离实际时间的可能性，直到有一天我的代码在生产中不断失败，因为时间签名是未来的。我花了一段时间才弄明白时间已经过了，当我谷歌了一下，发现时间同步是你在服务器上设置的。幸运的是，有一些直接而简单的解决方案，从那时起，我就将它包含在我的部署脚本中。

2.  这可能是糟糕的 api 设计的一个很好的例子。我集成了一个用于酒店搜索的第三方 api。这个 api 被设计成可以获取价格信息，但是使用酒店 ID 发出 GET 请求。搜索永远是一种获取‘我差不多就有这种设定在脑子里，所以没觉得有错。但是只有当我为伦敦的所有酒店(大约 650 家酒店)建立搜索查询时，我才发现哪里出了问题。HTTP 414 —请求 URL 太长。原来服务器对网址的长度有限制。[见此。](https://stackoverflow.com/questions/2659952/maximum-length-of-http-get-request)

你呢？在这里或在 twitter 上开始对话 [@srik_anth](https://twitter.com/srik_anth)