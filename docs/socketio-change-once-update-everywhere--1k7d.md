# Socket.io:改一次，处处更新

> 原文：<https://dev.to/kymiddleton/socketio-change-once-update-everywhere--1k7d>

在当今繁忙的世界中，与朋友或同事聊天的即时消息应用程序既省时又有趣。与通过电子邮件来回发送文档修订相比，允许协作的应用程序(如 Google docs)几乎可以即时保存数据更改，这是天才之举。当多个用户使用同一个应用程序时，内容以相同的方式显示，添加或更改会立即反映出来，这被认为是实时的。

当 JavaScript 库、Socket 和 web . exe 一起使用时，制作一个实时 web 应用程序并不困难。IO，已使用。插座。IO 库支持服务器和浏览器之间发生的实时通信事件。Node.js 有一个服务器端库和一个运行在浏览器中的客户端库，两者的 API 实际上是相同的。

**Socket IO** 建立在 WebSockets 之上，web sockets 是一种互联网协议，允许服务器和客户端之间的通信，它超越了典型的路由请求响应。使用 WebSockets，服务器可以发送路由请求响应，而无需客户端发起请求。套接字提供了客户端和服务器之间的双向通信通道。服务器可以将消息推送到客户端，当事件发生时，服务器获取消息并将其推送到连接的客户端。插座。IO 可以广播到多个套接字，存储与多个客户端相关的数据，或者用作 WebSockets 的包装器。重要的是要记住一些老的浏览器不支持 WebSockets。IO 为这种情况提供了回退机制，这是一个很好的特性。

作为初学者构建全栈应用需要基本了解几项:JavaScript、HTML、节点包管理器(npm)、Node.js 和 Express.js(也可以使用 Node HTTP 或 Node-server 端框架)。要开始安装 Express 和 Socket。IO 带有以下命令:

```
npm init 
```

回答完提示问题后，运行:

```
npm install --save express socket.io 
```

接下来，在一个 JavaScript 文件中实现已安装的包，比如`server.js`来概述 Express 和 Socket.IO.
的`require`语句

```
// Initialize Express
const app = express();

// Dependencies
const express = require(‘express’);
const server = require(‘http’).createServer(app);
const io = require(‘socket.io’)(server);

// Set the port for the server to listen on

// Use body-parser for handling form submissions

// Set up Express to handle data parsing
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

// Set the server to use the public directory for static assets
app.use(express.static(path.join(__dirname, 'public')));

// Socket.io Routes
require('./sockets/’<name of socket routes file> ')(io);

// Connect to database

// Start server on predefined PORT
server.listen(PORT, function(){
    console.log(`App is now listening on PORT ${PORT}`)
}) 
```

程序需要知道当一个连接建立时采取什么步骤，这可以在一个`sockets.js`文件中创建。

```
module.exports = function (io) {

    io.on('connection', function (socket) {

        //Socket Routes
            socket.on('new-todo', function(data){
            console.log(data);

            io.emit('emit-todo', data);
        socket.emit('emit-todo', data);
        })

        console.log('connected');
    })
} 
```

为`new-todo`建立新连接时，数据将显示在控制台上。注意`io.emit()`和`socket.emit()`之间的不同也很重要。`io`版本`io.emit()`发送给连接到服务器的每个人，而`socket`版本`socket.emit()`发送回发起客户端。

显然，这只是勾勒了一个套接字的开始框架。IO 应用程序然而，一旦理解了构建全栈应用程序的初始细微差别，Socket。IO 可以让 web 应用程序更上一层楼。

要了解更多信息，请查看以下资源:
[Socket。IO Docs](https://socket.io/docs/#What-Socket-IO-is)
[插座。IO 教程](https://www.tutorialspoint.com/socket.io/index.htm)
[ExpressJS 教程](https://www.tutorialspoint.com/expressjs/index.htm)