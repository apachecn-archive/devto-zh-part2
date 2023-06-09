# 推客定制认证

> 原文：<https://dev.to/kayis/pusher-custom-authentication-o0n>

上周，我参加了有史以来第一次开发竞赛，并提交了一个无服务器的多人点击器游戏。

### [要是能拿到你的❤️就太棒了&🦄在我的入门帖上](https://dev.to/kayis/startup-clix-serverless-pusher-contest-entry-written-in-javascript-1jai)

作为回报，我也想给你一些诀窍。

# 为推送器定制认证

Pusher 允许自定义授权者，可以为您节省大量请求。

## 什么

Pusher 的授权者只是一个以一个`context`、一个`socketId`和一个`callback`为参数的函数。

```
function customAuth(context, socketId, callback) {
  ... 
} 
```

Enter fullscreen mode Exit fullscreen mode

当你试图加入一个私有的或者在线的通道时，它被推送客户端调用。

常规实现向您的后端发送一个 HTTP 请求，您必须返回一个令牌，客户端将使用该令牌连接 Pusher。

`socketId`是客户端的当前套接字 ID。

认证完成后需要调用`callback`。

```
// The first argument needs to be false if everything went well
// and the second one needs to include the credentials from the server
callback(false, authCredentials);

// The first argument needs to be true if the authentication failed
// and the second one can be a error description
callback(true, errorText); 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么

每次客户端订阅私有或在线通道时都会调用 authoriser 函数，默认情况下，每次都会发送一个 HTTP 请求，因此，根据一个客户端在短时间内加入的通道数量，合并这些请求可能是一个好主意。

此外，在我创建的游戏中，客户端可以从服务器获得关于加入哪个频道的信息。因此，您最终会收到一个获取通道的请求和一个验证通道的请求。使用自定义授权器，您可以在选择通道的同一个请求中创建`authCredentials`。

## 如何

### 常规认证

常规认证过程如下:

1.  客户端连接到推送器并获得一个`socketId`
2.  客户端尝试订阅私有或在线频道
3.  客户端将其`socketId`和`channelName`发送给服务器(您的服务器，而不是推送服务器)
4.  服务器要求推送器 API 为`channelName`认证`socketId`
5.  Pusher API 创建了`authCredentials`,它被发送回客户端
6.  客户端使用`authCredenatials`来订阅频道

服务器端认证看起来像这样

```
const authCredentials = pusher.authenticate(
  socketId,
  channelName,
  {user_id: socketId}
); 
```

Enter fullscreen mode Exit fullscreen mode

参数可以通过 HTTP 查询参数或主体进入，而`authCredentials`需要通过 HTTP 发送回客户端。

### 自定义验证

一个自定义版本，就像我在游戏中使用的一样，看起来会有所不同。

以前

1.  客户端连接到推送器并获得一个`socketId`
2.  客户端向服务器请求一个`channelName`
3.  客户端从服务器获得一个`channelName`
4.  客户端尝试使用`channelName`订阅推送频道
5.  客户端从服务器获取`authCredentials`
6.  客户端通过`authCredentials`订阅推送频道

在...之后

1.  客户端连接到推送器并获得一个`socketId`
2.  客户端向服务器请求一个`channelName`
3.  客户端从服务器获得一个`channelName`和`authCredentials`
4.  客户端通过`authCredentials`订阅推送频道

所以我们需要两个新零件。一个新的授权者，它不会调用服务器，而是使用一些本地数据进行认证，并在一个请求中从服务器获取`channelName`和`authCredentials`。

让我们从后面开始，如何从服务器获取这两个信息。

为此，我们可以向 Pusher 客户端添加一个新方法。

```
pusher.subscribeServerChannel = function() {
  const {socket_id} = pusher.connection;

  return fetch("/getChannel?socketId=" + socket_id)
  .then(r => r.json())
  .then(({channelName, authCredentials}) => {
    // used by the authoriser later
    pusher.config.auth.preAuth[channelName] = authCredentials;

    // calls the autoriser
    return pusher.subscribe(channelName);
  })
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数尝试订阅它从服务器(您的后端)请求的频道。`GET /getChannel`端点需要`socketId`来创建`authCredentials`，然后`channelName`也将在服务器端创建。

接下来，我们需要新的授权人，也是客户端。

首先获取旧的，然后将新的添加到其中。在之前的所有*我们创建一个连接。* 

```
const supportedAuthorizers = Pusher.Runtime.getAuthorizers();

supportedAuthorizers.preAuthenticated = function(context, socketId, callback) {
  const { authOptions, channel } = this;

  // getting the credentials we saved in subscribeServerChannel
  const authCredentials = authOptions.preAuth[channel.name];

  if (authCredentials) return callback(false, authCredentials);

  callback(true, "You need to pre-authenticate for channel: " + channel.name);
};

Pusher.Runtime.getAuthorizers = () => supportedAuthorizers;

// Later when the connection is created

const pusher = new Pusher(APP_KEY, {
  auth: {
    preAuth: {} // where the auth credentials will be stored
  },
  // set the transport to the new authoriser
  authTransport: "preAuthenticated",
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，创建`channelName`并处理认证的服务器端点。

```
server.get("/getChannel", (req, res) => {
  const {socketId} = req.query;
  const channelName = "private-" + Math.random();

  const authCredentials = pusher.authenticate(socketId, channelName, {user_id: socketId});

  res.send({channelName, authCredentials});
}); 
```

Enter fullscreen mode Exit fullscreen mode

在客户端，我们现在可以简单地调用`subscribeServerChannel`并得到一个推送通道作为回报。

```
 const pusher = new Pusher(APP_KEY, {
    auth: { preAuth: {} },
    authTransport: "preAuthenticated",
    ...
  });

  pusher.connection.bind("connected", async () =>
    const channel = await pusher.subscribeServerChannel();
    channel.bind("my:event", ...);
  ); 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是这样。

您只需发出一个请求，就可以获得加入客户端通道所需的所有数据。

## 结论

Pusher 客户端是一个非常灵活的软件，允许您根据自己的喜好修改认证流程。这大大简化了集成，并且从长远来看允许一些性能调整。

## 竞赛

另外，如果你喜欢这篇文章:

### [我很欣赏你的❤️ &🦄在我的入门帖上](https://dev.to/kayis/startup-clix-serverless-pusher-contest-entry-written-in-javascript-1jai)