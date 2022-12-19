# int 与 short 以及(意外的)性能影响

> 原文：<https://dev.to/joaofbantunes/til-int-vs-short-and-the-unexpected-performance-impact-1ij4>

有一天，在工作中检查一些代码时，我注意到一个同事做了一个 for 循环，将迭代变量显式定义为一个`short`(也就是一个`Int16`)，而不是通常的`int`(或`Int32`)(或者在我的例子中是`var`，反正最后是一个`int`)。

类似于以下内容，仅供参考: