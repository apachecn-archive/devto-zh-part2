# 如何用 Python 创建流 HTTP 接口？

> 原文：<https://dev.to/anoff/how-to-create-a-streaming-http-interface-in-python--ceh>

嘿伙计们，

最近我一直在玩人工智能和 Python。我现在想建立的是一个基于 python 的 ML 模型(keras 等)的 web 界面。为了充分的灵活性，我希望应用程序 a)采取输入数据流 b)返回输出数据流。输入流中的每个块都将通过 ML 模型运行，相应的输出将作为一个块流回。

然而，我在网上找不到任何资源告诉我如何在生产级 python web 框架中发送流响应。有人知道如何用 python 发送 HTTP 流响应吗？

目前我正致力于结合 falcon + gunicorn 来创建一个 web 应用程序，因为 Flask 似乎不是一个产品级框架。虽然我的要求不是生产级的，但我很想知道如何在这样的级别上做到这一点。

/安德烈亚斯