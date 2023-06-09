# 让 qmux 使用浏览器和节点 WebSocket

> 原文：<https://dev.to/progrium/getting-qmux-to-use-browser-and-node-websocket-64a>

今天我们回到 qmux:一个协议、C 库和用于复用流的原生 JavaScript 库。我想完成 JavaScript 库，它需要一个适用于节点和浏览器的 WebSocket 会话传输。

会话传输并没有那么难。我曾计划使用一个不同的 Node WebSocket 库，但它运行得相当好。浏览器版本几乎相同，因为它们的 API 非常接近。

真正的诀窍在于弄清楚如何为节点和浏览器编译和打包。TypeScript 添加了一个额外的配置层。我过去常常通过根据是否检测到浏览器来不同地公开 API 来编写适用于浏览器和节点的库。这个项目稍微复杂一些，因为它有内部需求，并且 TypeScript 有自己的导入/导出内容。

我最终使用了核心 qmux 文件，然后是一个节点文件和一个浏览器文件。对于节点，我们指向节点文件作为包的索引。对于浏览器，我们使用 [Browserify](http://browserify.org/) 和 [Uglify](https://github.com/mishoo/UglifyJS2) 来制作一个独立的、小型化的 JavaScript 文件以包含在页面中。这也迫使我稍微清理一下项目目录结构。

我觉得这很有趣，因为我只想导出多个输入文件中的最后一个，所以我在特定于浏览器的输入脚本中重新导出了核心类型。由于我们经常使用 Node 的 Buffer 类，所以我最后还打包了一个为浏览器设计的小缓冲实现。我再次导出它，因为我们希望用户使用它。

我想在下一个“q”项目系列中，我们会尝试让 qrpc 在浏览器中工作。然后我们会做饭。我还在探索如何让 qmux 和 qrpc 在 JXA 环境中工作。

[https://www.youtube.com/embed/EI3-KWngQf0](https://www.youtube.com/embed/EI3-KWngQf0)