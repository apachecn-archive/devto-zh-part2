# 不需要 express 就能入门 socket.io

> 原文：<https://dev.to/sadick/you-dont-need-express-to-get-started-with-socketio-4ieg>

开始使用 socket.io 最好的地方是他们的网站。但是一旦你访问它，你会看到一个示例应用程序，演示如何使用 socket.io。

```
var app = require('express')();
var http = require('http').Server(app);
var io = require('socket.io')(http);

app.get('/', function(req, res){
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', function(socket){
  console.log('a user connected');
});

http.listen(3000, function(){
  console.log('listening on *:3000');
}); 
```

如果您对 node 相当陌生，这会让您认为使用 socket.io 需要 express。在上面的示例中，它们包括 express 来提供索引文件，该文件将是您的 socket 服务器的客户端应用程序。上面的方法没有错，但是如果我们说你不知道 express，现在你在使用 socket.io 之前还有一件事要学。

如果他们的例子中没有引入额外的库，那就更好了。Socket.io 有另一个使用节点 http 服务器的例子

```
var app = require('http').createServer(handler)
var io = require('socket.io')(app);
var fs = require('fs');

app.listen(80);

function handler (req, res) {
  fs.readFile(__dirname + '/index.html',
  function (err, data) {
    if (err) {
      res.writeHead(500);
      return res.end('Error loading index.html');
    }

    res.writeHead(200);
    res.end(data);
  });
}

io.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
}); 
```

但即使这样也有点过分了。我更喜欢服务器和客户端分开。因此，这将是我的服务器。

```
const io = require("socket.io");
const server = io.listen(3000);

server.on("connection", function(socket) {
  console.log("user connected");
  socket.emit("welcome", "welcome man");
}); 
```

客户端将包括 socket.io 客户端库和普通的 html。

```
<html>
    <head>
        Socket io client
        <script src="http://localhost:3000/socket.io/socket.io.js"></script>
        <script>
            var socket = io("http://localhost:3000");
            // use your socket
            socket.on("welcome", (message) => {
                // do something with the message.
            })
        </script>
    </head>
    <body>
    </body>
</html> 
```