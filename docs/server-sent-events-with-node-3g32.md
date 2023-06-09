# 服务器发送的带有节点的事件

> 原文：<https://dev.to/jbutz/server-sent-events-with-node-3g32>

服务器发送事件(SSEs)允许从服务器到客户端的单向通信。它们对于通知或活动订阅源非常有用。我最近在一个项目中使用它们在浏览器中显示后台进程的输出。

在浏览器中，使用 [`EventSource`](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) 接口连接到服务器，并添加事件监听器。这真的很容易。

```
const sseSource = new EventSource('/event-stream');
sseSource.addEventListener('message', (e) => {
const messageData = e.data;
// ...
// ...
});
// When finished with the source close the connection
sseSource.close(); 
```

Enter fullscreen mode Exit fullscreen mode

服务器端的事情稍微复杂一点，但不会太多。只是需要做一些非常具体的事情。HTTP 连接需要保持打开。我看到了很多包含`req.socket.setTimeout(Infinity)`的例子，但这是不必要的，至少在节点 v8 和更高版本中会抛出一个错误。默认情况下，连接在节点端保持打开。您应该发送一个`Connection: keep-alive`头来确保客户端也保持连接打开。应该发送带有值`no-cache`的`Cache-Control`报头，以阻止数据被缓存。最后，需要将`Content-Type`设置为`text/event-stream`。

完成所有这些后，应该向客户端发送一个新行(`\n`)，然后就可以发送事件了。事件必须以字符串的形式发送，但字符串中的内容并不重要。JSON 字符串非常好。
事件数据必须以`data: <DATA TO SEND HERE>\n`格式发送。`data:`部分很重要，因为您可以为事件提供 id 和类型。两者兼而有之的例子可能是这样的:

```
id: 42
event: deepThoughtOutput
data: I have finished computing the answer 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，每一行的末尾都应该有一个换行符。为了表示事件的结束，还需要添加一个额外的换行符。对于上面的例子，`EventSource`监听器应该连接到`deepThoughtOutput`事件，而不是`message`事件。
多条数据线完全没问题，下面这条工作正常。

```
data: [
data: "Array Element 1",
data: "Array Element 2",
data: ] 
```

Enter fullscreen mode Exit fullscreen mode

当使用 IDs 时，您可能会遇到`Last-Event-ID` HTTP 头。如果连接中断，客户端将在`Last-Event-ID`报头中发送它收到的最后一个 ID，以允许事件从中断的地方恢复。请特别注意您在该区域使用的任何 polyfill 库。有些使用查询字符串而不是头。

下面是一个使用 SSEs 的节点应用程序的例子。

```
const express = require('express');
const app = express();
function sseDemo(req, res) {
let messageId = 0;
const intervalId = setInterval(() => {
res.write(`id: ${messageId}\n`);
res.write(`data: Test Message -- ${Date.now()}\n\n`);
messageId += 1;
}, 1000);
req.on('close', () => {
clearInterval(intervalId);
});
}
app.get('/event-stream', (req, res) => {
// SSE Setup
res.writeHead(200, {
'Content-Type': 'text/event-stream',
'Cache-Control': 'no-cache',
'Connection': 'keep-alive',
});
res.write('\n');
sseDemo(req, res);
});
app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

注意请求的`close`事件处理程序。如果你需要清理任何东西，这是做它的地方。在这个例子中，我停止了间隔计时器，这样它就不会在服务器上继续不必要的运行。

当我实现 SSEs 时，我是在别人构建的应用程序模板的基础上实现的，而不是从一个例子中获得的。我遇到了一个奇怪的问题，我的事件只有在连接关闭后才能被客户端接收到。经过一番挖掘，我发现这是由于压缩设置。

[`compression` npm 包](https://www.npmjs.com/package/compression)查看 mime 类型以确定是否应该压缩响应。默认情况下，所有的 mime 类型都是压缩的，这意味着正在发送的事件被缓冲以便压缩。我从来没有得到足够的缓冲区来刷新它。如果你遇到这种情况，你有几个选择。您可以禁用对`text/event-stream` mime 类型的压缩，禁用对 SSE 端点的压缩，或者您可以在每个事件之后调用`req.flush()`来刷新缓冲区并将您的事件发送到客户端。

另一个重要的信息是认证。无法通过`EventSource`发送自定义标题。如果你需要向服务器传递一个令牌，你应该使用 cookies。在我的例子中，当用户进行身份验证时，我在服务器上设置了一个`HttpOnly` cookie，然后使用它来验证用户的身份以处理事件。

如果您的客户端和服务器不在同一个地方，那么您将需要考虑一些 CORS 问题，我不会在这里介绍。如果你使用负载平衡器或代理，你还需要确保它不会过早关闭连接。客户端会尝试重新连接，但是频繁的重新连接会增加额外的开销。

如果您想更详细地了解服务器发送事件，下面是一些附加资源。

[Mozilla 开发者网络-使用服务器发送事件](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)
[WHATWG 服务器发送事件生活水准](https://html.spec.whatwg.org/multipage/server-sent-events.html)
[html 5 Rocks event source 教程](https://www.html5rocks.com/en/tutorials/eventsource/basics/)
[服务器发送事件 with Node.js (Express)教程](https://tomkersten.com/articles/server-sent-events-with-node/)