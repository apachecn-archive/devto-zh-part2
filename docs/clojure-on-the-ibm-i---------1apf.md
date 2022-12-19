# IBM i 上的 Clojure

> 原文：<https://dev.to/jreighley/clojure-on-the-ibm-i---------1apf>

我一直在使用 Clojure 来制作与 IBMi 服务器对话的 API。

Clojure 很好，因为它与 Java 很好地互操作，所以它可以通过 [JT400 工具包](https://javadoc.midrange.com/jtopen/index.html?com/ibm/as400/access/AS400.html)访问 IBMi。

我为运行 IBMi 服务器的各种客户机提供大量支持。我过去不得不手动、串行和异步地进行许多故障排除练习。连接到客户端，运行报告，扫描结果，检查这里的常数，检查那里的常数-验证某些作业正在运行，没有消息等。

我现在可以将浏览器指向一个 URL，我的 Clojure 程序将运行整个故障诊断树，并向我呈现一个显示所有结果的网页。通常我甚至可以在问题旁边加上一个“修复按钮”。

如果你感兴趣，这里有一个非常简单的入门要点: