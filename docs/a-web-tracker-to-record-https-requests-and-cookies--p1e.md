# 记录 HTTP/S 请求和 cookies 的网络跟踪器。

> 原文：<https://dev.to/jaydeepborkar/a-web-tracker-to-record-https-requests-and-cookies--p1e>

几周前，我正在为构建一个网络跟踪器做贡献，以记录来自浏览器(仅限于 Google Chrome)的 HTTP/S 请求和 cookies。一个在点击时记录用户会话的扩展，用户可以根据自己的意愿在点击时停止记录，然后它会被转换成 JMeter 脚本并可用于负载测试。市场上所有的扩展，它们只记录单个标签的用户会话。有没有可能从多个选项卡中集成用户会话，并使用它们进行测试？有意义吗？