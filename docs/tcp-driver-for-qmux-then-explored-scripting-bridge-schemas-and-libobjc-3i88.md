# qmux 的 TCP 驱动程序，然后研究了脚本桥模式和 libobjc

> 原文：<https://dev.to/progrium/tcp-driver-for-qmux-then-explored-scripting-bridge-schemas-and-libobjc-3i88>

今天我回到了我的 Node.js 和浏览器的 qmux 实现。因为已经有一段时间了，qmux 是从 SSH 中提取的流复用协议。我主要将它用于 qrpc，这是我的分布式对象系统的基础。

今天的目标是使用 Node 的标准库编写一个 TCP 连接驱动程序。我喜欢有一个不需要任何库就能使用的 TCP 实现。

我已经有一段时间没有使用 Node net 模块了，这并不有趣。我最后参考了一些用 promise APIs 包装它们的库，因为我的系统是围绕 async/await 设计的，并承诺基本健全。

我得到了一个工作的 TCP qmux 演示，然后我在 qrpc 下测试了它。它不起作用，但是这个问题最终是我在使用二进制数据缓冲区的 qmux 实现中发现的。正如您所想象的，这在 JavaScript 中相当棘手。

所以，后来我决定回去用一个看起来更流行的 Node.js 库围绕 WebSocket 构建一个更好的驱动程序。我仍然很惊讶它们不是标准库的一部分。在玩了一会儿之后，我决定当我回来的时候，我将只使用我更熟悉的库；其实没那么重要。

这有很多用途，但我希望能够使用 qrpc 从 JXA 应用程序进行交流。在其他想法中，我可以在 JXA 环境中导出桥接的 Objective-C API；有点像 Python 和 Ruby Objective-C 桥。考虑到这一点，我开始寻找所有苹果框架附带的桥支持文件。果然，他们有描述整个 API 的巨大 XML 文件。

一些现有的桥梁有关于这种格式的有用文档，我最终找到了苹果官方文档。这让我发现了 libobjc。我想我上次读到脚本桥的时候就知道这一点了，但是通过 FFI 看到它的使用让我意识到拥有一个面向对象运行时的 C 库接口是多么酷。

我认为还没有围棋桥是不可能的。所以，我比上次挖了更多。我发现有几个项目都超过 5 年了还没有完成。其中一个为 libobjc 提供了非常好的 Go API 包装，但是所有的框架 API 都是手工添加的。我想我可以很容易地用脚本桥模式生成这些 API。

然后，我想有趣的是，如果我可以使用 Go 的 Objective-C API，我就不会对使用 JXA 那么感兴趣了。AppleScript APIs 仍然很好，通过 JXA 的 qrpc 重新公开它们可能仍然是最容易的。我们走着瞧。

[https://www.youtube.com/embed/zjj_oXRk5A4](https://www.youtube.com/embed/zjj_oXRk5A4)