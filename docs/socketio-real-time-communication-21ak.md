# 插座。IO:实时通信

> 原文：<https://dev.to/rmadisetti3/socketio-real-time-communication-21ak>

[![](../Images/a6b31295631ad723e6b07c6cd8b89a4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iY0HmwcV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7brapfmfwzs9cmv1zj6w.jpg) 
各位 Javascript 程序员好，

本文将解释套接字。IO 及其在全栈 web 应用程序中的优势，而不是传统的冗长的数据库方法。

首先，插座。IO 是一个 Javascript 库，有助于客户端和服务器之间的实时通信。该功能是任何依赖于数据流、消息传递、同步群组协作甚至游戏的应用程序不可或缺的组成部分。插座。IO 由允许这种即时通信的两个部分组成:浏览器中的客户端库和 Node.js 中的服务器端库。套接字在交互的这两个方面之间提供了一个双向通道，允许客户端推送到服务器，并在很短的时间内接收向所有连接的客户端发出的响应。由于这种高效的功能，许多流行的应用程序都使用它，如 Microsoft Office、Trello 和 Zendesk。

现在，为了实现套接字。IO，请遵循以下步骤。首先，我们需要使用节点包管理器(npm)安装 express 和 socket.io。
`npm init`
对终端询问的所有问题输入 yes。然后，运行:
`npm install --save express socket.io`
这将安装正确运行 Socket.IO 所需的所有包，接下来，我们需要使用已安装的包实现应用程序。在一个. js 文件中，使用如下:
`const express = require('express');
const app = express();
const server = require('http').createServer(app);
const io = require('socket.io')(server);
const socket = io();`
接下来，我们需要告诉程序当一个连接建立后要做什么。使用此命令:
`module.exports = function(io) {
io.on('connection', function(socket) {
//SOCKET ROUTES
socket.on('new-data', function(data) {
console.log(data);
}
})
};`
现在，每当连接‘new-data’建立时，数据将记录在控制台中。不错！

这个框架只概述了一个套接字的开始。IO 应用程序，但在创造性和实用性方面还有很多事情要做。