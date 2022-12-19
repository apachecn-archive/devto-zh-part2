# 全栈开发者之旅。2

> 原文：<https://dev.to/tnguyen303/journey-to-a-full-stack-developer---ep-2-4he4>

本周，我们学习了如何制作一个实时对话的应用程序。这里使用的技术叫做 socket.io，被脸书、Instagram、Youtube、Google Docs 广泛使用。所有这些网站(或应用程序)都会弹出实时更新。这看起来好像是内置的，但实际上，需要安装一些编码和一个包才能让它这样工作。

Socket.io 必须在服务器(后端)上运行。Socket.io 并不直接与你的数据库交互，比如 Mongo 或者 mySQL，而是一种向前端传递和呈现数据的方式。因此，仍然需要所有的 ajax 调用。

要设置使用 socket.io，请确保在 js 服务器文件中具有:

```
const express = require("express");
const app = express();
//http package lets you run server without Express, socket.io requires more control that is provided with this package
const server = require("http").createServer(app);
const io = require("socket.io")(server);

require("./sockets/todolist-sockets")(io); 
```

如您所见，我们只添加了两行来设置它。在 socket.io 中，我们有两个主要命令:

1.  发出——这只是一个通知，告诉服务器有新数据，以及新数据
2.  on——这基本上是一个事件监听器，监听特定的“发射”,然后执行脚本，通常是向用户呈现新数据。

下周我们将全面介绍代码，以便您可以在自己的应用程序中实现它。一如既往，请让我知道我很乐意与你讨论的任何意见。