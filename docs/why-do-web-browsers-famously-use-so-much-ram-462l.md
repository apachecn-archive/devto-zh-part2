# 为什么网络浏览器使用这么多内存是出了名的？

> 原文：<https://dev.to/nektro/why-do-web-browsers-famously-use-so-much-ram-462l>

是因为字符串中的每个字符都是 UTF-16，每个数字都是 float64，也就是平均页面中的对象数量，还是因为 JS 是一种 VM 语言？还有别的吗？

@ see
[https://tc39 . github . io/ECMA 262/# sec-terms-and-definitions-string-value](https://tc39.github.io/ecma262/#sec-terms-and-definitions-string-value)
[https://tc39 . github . io/ECMA 262/# sec-terms-and-definitions-number-value](https://tc39.github.io/ecma262/#sec-terms-and-definitions-number-value)