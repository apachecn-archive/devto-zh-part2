# 您使用什么工具来生成服务器端 PDF？

> 原文：<https://dev.to/christianca/what-tools-do-you-use-for-server-side-pdf-generation-31mb>

嘿，我长话短说。在我们公司，多年来我们尝试了不同的 PDF 生成方法(服务器端，特别是 PHP ),但没有一种让我们满意。HTML 到 PDF 在前面使用打印样式表(现在看起来像一个低技术的 puppetteer)，domPDF 在后面使用 HTML 到 PDF，或者 FPDF/TCPDF 在后面手动放置每个页面上的元素。

对于简单的类似发票的 pdf，我们可以使用任何东西，但目前我面临的任务是生成一个具有复杂设计和动态生成数据的文档。为此，我使用 FPDI + TCPDF 加载一个预生成的 PDF，然后使用坐标填充空白，这真的很无聊和麻烦。

有什么想法吗？

谢谢！