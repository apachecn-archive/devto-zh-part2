# Python 中的 web 功能 API 客户端

> 原文：<https://dev.to/k4ml/webfaction-api-client-in-python-db2>

自从他们被称为 python-hosting.com 后，我就一直关注着他们。回到 2000 年初，要找到任何 python web 托管都不容易。Textdrive 是第一家，但关闭后，我就在网上四处寻找新的主机提供商。

从那以后，我就一直在主持 web inflation 上的所有附带项目。

我发现了用于 web fabrication API 的 cli 工具。这个项目看起来已经被放弃了，所以我把它放到了我 github 账户上，并开始添加一些未实现的命令。

*   [https://pipi . python . org/pipi/webf](https://pypi.python.org/pypi/webf)
*   [https://github.com/k4ml/webf](https://github.com/k4ml/webf)

在使用 API 一段时间后，我意识到我们并不真的需要花哨的库来访问 API。很简单，API 只接受普通字符串、布尔或列表作为参数，并返回一个字典或字典列表。所以这里一个客户端只用了 106 行代码就完成了整个 API-[https://gist.github.com/k4ml/5889346#file-webf-py](https://gist.github.com/k4ml/5889346#file-webf-py)。