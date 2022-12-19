# 节点 Js 中的简单 HTTP/HTTPS 代理

> 原文：<https://dev.to/nimit95/a-simple-http-https-proxy-in-node-js-3810>

代理是互联网上你和你的预定主机之间的一个简单的服务器。代理主要有两种，**正向代理**和**反向代理。**

**转发代理**

大多数情况下，简单术语“代理”指的是正向代理。它只是位于客户机和服务器之间，充当中继代理。它们通常由客户端或内部网络从客户端放置。可以有各种使用转发代理的情况，如跟踪请求、响应、拒绝对某些域的访问、修改报头、改变位置等。

<figure>[![](../Images/27c5f3656220791242f8c56feede95c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9eVR9yku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/539/1%2AB390-BMZ4Tqcc7l6YUgX7g.png) 

<figcaption>转发代理</figcaption>

</figure>

**反向代理**

服务器使用这些类型的代理，主要是为了安全和负载平衡。客户端访问这些反向代理服务器，而不是实际的服务器，然后这些代理服务器向实际的服务器发送请求。对于 web 服务器来说，这种架构有几个好处。它可以将恶意用户拒之门外，平衡服务器之间的负载，并通过缓存静态内容来减少源服务器上的负载。

<figure>[![](../Images/8f83133158485ae33077fc1eb364ac5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cA_2B30V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/506/1%2AlfyfY68D1wayyIS1qRk8WA.png) 

<figcaption>反向代理</figcaption>

</figure>

### 入门

理论讲够了，让我们动手制作一个 HTTP/HTTPS 转发代理。

我们将在节点中使用 net 模块。我假设对 NodeJS 和 net 模块有基本的了解。如需参考，请查看官方文档[https://nodejs.org/api/net.html](https://nodejs.org/api/net.html)

我们首先创建一个简单的网络服务器，它监听端口 8124，这个服务器将作为客户端连接的代理服务器。

```
const net = require('net');
const server = net.createServer();

server.on('connection', (clientToProxySocket) =\> {
 console.log('Client Connected To Proxy');
});

server.on('error', (err) =\> {
 console.log('SERVER ERROR');
 console.log(err);
});

server.on('close', () =\> {
 console.log('Client Disconnected');
});

server.listen(8124, () =\> {
 console.log('Server runnig at [http://localhost:'](http://localhost:') + 8124);
}); 
```

当一个客户端连接到我们的服务器时，我们在回调中得到一个套接字，它是客户端和服务器之间的套接字。

### 解析 HTTP/HTTPS

现在 HTTP 和 HTTPS 是不同的协议，所以我们将分别处理这两种情况。连接之后，我们将只需要第一个数据包数据来获取主机详细信息。所以我们在数据回调中使用 once 来获取第一个数据。

对于 HTTP，请求包含一个主机参数，HTTP 的端口是 80。你可以在 https://hpbn . co/brief-history-of-http/# http 11-internet-standard 上了解更多信息

在 HTTPS 的情况下，由于 SSL 加密，我们无法读取数据包，因此不可能从数据包中读取主机。但是在实际请求之前，有一个连接请求，其中包含主机和端口将是 443。要阅读更多信息，请查看 RFC[https://tools.ietf.org/html/rfc7231#section-4.3.6](https://tools.ietf.org/html/rfc7231#section-4.3.6)

### 服务器连接

获得主机名后，我们使用 net.createConnection()连接到服务器。它需要两个参数，主机和端口来连接，第二个是连接的回调。连接完成后，我们只需将 clientToProxySocket 通过管道传输到 proxyToServerSocket。套接字是从流中派生出来的，因此它们可以通过管道传输。要了解更多关于溪流和管道的信息，请参考[本](https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93)。TL；管道博士是

```
**readableSrc**.pipe( **writableDest** ) 
```

在连接到我们的代理—
之后，最终的代码看起来像

```
**server.on** ('connection', (clientToProxySocket) =\> {
 console.log('Client Connected To Proxy');
 // We need only the data once, the starting packet
**clientToProxySocket.once** ('data', (data) =\> {
 let isTLSConnection = data.toString().indexOf('CONNECT') !== -1;

 //Considering Port as 80 by default 
 let serverPort = 80;
 let serverAddress;
 if (isTLSConnection) {
 // Port changed to 443, parsing the host from CONNECT 
 serverPort = 443;
 serverAddress = data.toString()
 .split(' **CONNECT**')[1]
 .split(' ')[0].split(':')[0];
 } else {
 // Parsing HOST from HTTP
 serverAddress = data.toString()
 .split('Host: ')[1].split('\r\n')[0];
 }

let proxyToServerSocket = **net.createConnection** ({
 host: serverAddress,
 port: serverPort
 }, () =\> {
 console.log('PROXY TO SERVER SET UP');

 if (isTLSConnection) {
 //Send Back OK to HTTPS CONNECT Request
 clientToProxySocket.write('HTTP/1.1 200 OK\r\n\n');
 } else {
 proxyToServerSocket.write(data);
 }
 // Piping the sockets
**clientToProxySocket.pipe(proxyToServerSocket);  
 proxyToServerSocket.pipe(clientToProxySocket);** proxyToServerSocket.on('error', (err) =\> {
 console.log('PROXY TO SERVER ERROR');
 console.log(err);
 });
 });
 clientToProxySocket.on('error', err =\> {
 console.log('CLIENT TO PROXY ERROR');
 console.log(err);
 });
 });
}); 
```

### **测试代理**

代理作为普通节点服务器- node server.js 启动

现在，您可以将系统或浏览器代理设置为 127.0.0.1(本地主机)和端口 8124。在 Firefox 上，你可以在网络设置中设置代理，在 Chrome 上，你需要设置一个系统范围的代理。代理设置后，如果您尝试在浏览器上打开某个站点，您将能够看到主机和其他日志，您将在日志中启用这些日志。您可以尝试使用代理。

代码的最终回购可以在下面看到—

[nimit95/Forward-Proxy](https://github.com/nimit95/Forward-Proxy)

如有任何疑问，可以通过 twitter [@nimit95](https://dev.to/nimit95) 与我联系。