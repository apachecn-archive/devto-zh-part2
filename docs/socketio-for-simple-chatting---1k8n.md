# 简单聊天应用的 Socket.io

> 原文：<https://dev.to/moz5691/socketio-for-simple-chatting---1k8n>

在一个基本的聊天应用程序中，用户首先进入聊天室。用户首先会收到欢迎消息。

服务器端只对新进入的用户“问候消息”。用户显然不希望在第一条欢迎消息之后被问候。用户开始向另一个用户发送消息，但不想向所有人广播。

“io”是一个套接字。连接到侦听传入事件的实例的 IO 服务器实例。

io 变量代表一组插座。套接字变量仅用于与每个单独的连接进行通信。

socket.io 函数列表

```
socket.emit('message', "this is a test"); //sending to sender-client only
socket.broadcast.emit('message', "this is a test"); //sending to all clients except sender
socket.broadcast.to('game').emit('message', 'nice game'); //sending to all clients in 'game' room(channel) except sender
socket.to('game').emit('message', 'enjoy the game'); //sending to sender client, only if they are in 'game' room(channel)
socket.broadcast.to(socketid).emit('message', 'for your eyes only'); //sending to individual socketid
io.emit('message', "this is a test"); //sending to all clients, include sender
io.in('game').emit('message', 'cool game'); //sending to all clients in 'game' room(channel), include sender
io.of('myNamespace').emit('message', 'gg'); //sending to all clients in namespace 'myNamespace', include sender
socket.emit(); //send to all connected clients
socket.broadcast.emit(); //send to all connected clients except the one that sent the message
socket.on(); //event listener, can be called on client to execute on server
io.sockets.socket(); //for emiting to specific clients
io.sockets.emit(); //send to all connected clients (same as socket.emit)
io.sockets.on() ; //initial connection from a client. 
```

用户输入从简单的 HTML 输入表单开始。

[![image](img/764750fb24f2a5c5c71c8cf89a6e4fca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GYhxeNPY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlhtbv4j7to9ojrml7x2.png)

你可以从三个地方入手。

客户端:来自 HTML 表单的输入
客户端:客户端的事件监听器，带有客户端 socket.io，
服务器:接收消息，并通过服务器端 socket.io 发送给客户端。

在下面的示例中，“sendMessage”通道是从客户端到服务器的。“接收消息”是从服务器到客户端的。服务器通过 sendMessage 从客户端接收消息，并通过 receiveMessage 通道发送回所有客户端。

它最初看起来可能令人困惑，但它非常简单。

客户端->发送消息->服务器
服务器< -接收消息-服务器

```
Client side, HTML 
 id="messages" is to display broadcasted messages. 
 id ="message-form" is to hold input-field.   

<ol id="messages"></ol>
<form action="" id="message-form">
<input type="text" name="message" id="message" placeholder="enter message">
<button>Send</button>
</form> 
```

```
Client side, JS
// First entered message is emitted after button click event.  This message is sent through "sendMessage" channel.  Server side will receive message through "sendMessage" channel, then, it emits to all clients connected to the server.

$('#message-form').on('submit', function(event) {
    event.preventDefault();
    socket.emit(
        'sendMessage',
    {
        from: 'User',
        text: $('#message').val()
    },
    () => {//callback function here }
  );

// Client side event listener.  It takes new messages from the server and appends it to HTML. 
socket.on('receiveMessage', message => {
    console.log('receiveMessage -- client side', message);
    const li = $('<li></li>');
    li.text(`${message.from}: ${message.text}`);
    $('#messages').append(li);
}); 
```

```
Server side, JS : 
// io.on from server side listens all incoming events.   Socket.emit inside io.on emits only "receiveMessage" message to all clients connected to the server.   Socket.emit only emit to sender-client while Socket.broadcast sends to every client except the sender itself.

io.on('connection', socket => {
    console.log('New user connected -- msg from server');
    /* socket.emit does Welcome message to new chatter */
    socket.emit('newMessage', chatMessage('Chatbot', 'Welcome'));
    /* socket.braodcast.emit from Admin to new user joined. */
    socket.broadcast.emit(
        'newMessage',
        generatedMessage('Chatbot', 'New User joined')
    );
     /* socket.on listens "sendMessage" from client and io.emit sends the message out to clients */
    socket.on('sendMessage', (message) => {
    console.log('Send message -- server side', message);
    io.emit('receiveMessage', chatMessage(message.from, message.text));
    });

const chatMessage = (from, text) => {
    return {
        from,
        text,
        time: new Date().getTime()
   };
}; 
```

您可以将聊天记录添加到数据库中，如 Mongo 或 SQL。计划以后再添加。

参考…
[https://stack overflow . com/questions/32674391/io-emit-vs-socket-emit](https://stackoverflow.com/questions/32674391/io-emit-vs-socket-emit)