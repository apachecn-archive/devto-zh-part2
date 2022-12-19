# Apache Thrift over ZeroMQ

> 原文：<https://dev.to/jeikabu/apache-thrift-over-zeromq-66e>

我们客户端的后台服务部分([服务](//./windows-service-in-c-1m79)、[第 0 层](//./more-windows-service-in-c-3keg)、[第 1 层](//./layer1-17dj))最初为 RPC 使用节俭传输，为[发布/订阅](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)使用 [ZeroMQ](http://zeromq.org/) (通过 [NetMQ](https://github.com/zeromq/netmq/) )。因此，我们有大量的端口/连接。

将节俭客户与 [TMultiplexedProtocol](https://github.com/apache/thrift/blob/master/lib/netcore/Thrift/Protocols/TMultiplexedProtocol.cs) 复用有所帮助。我们的技术总监有了用 ZeroMQ 本身作为节俭交通工具的想法。实际上，在 ZeroMQ 上复用我们所有的通信代理。

我们还利用 [Majordomo 协议](https://rfc.zeromq.org/spec:7/MDP/)(来自 [NetMQ 示例](https://github.com/NetMQ/Samples/tree/master/src/Majordomo))在 ZeroMQ 之上提供额外的面向服务的功能。

## 运输

```
public class ZeroMqTransport : TStreamClientTransport
{
    public string Channel { get; set; }
    private MDPClient Client { get; set; }

    public ZeroMqTransport(MDPClient client)
    {
        Client = client;

        InputStream = null; // only set input stream after received data
        OutputStream = Common.MemoryPool.GetStream(); // set output stream so we can write message into it
    }

    public override async Task FlushAsync(CancellationToken token)
    {
        await base.FlushAsync(token);

        try
        {
            var ostream = OutputStream as MemoryStream;
            if (ostream.Length <= 0)
                return;

            InputStream = null;
            NetMQMessage req = new NetMQMessage();
            req.Append(ostream.ToArray());

            // TODO: rep could be null if time out happens
            var rep = Client.Send(Channel, req);

            InputStream = rep == null ? null : Common.MemoryPool.GetStream(rep.Last.Buffer);
            OutputStream = Common.MemoryPool.GetStream();
        }
        catch (Exception ex)
        {
            Logging.Logger.Log($"zmq transport flush: {ex.Message}", Logging.LogLevel.Warn);
            Logging.Logger.Log($"zmq transport flush: {ex.Message} \n {ex.StackTrace}", Logging.LogLevel.Debug);
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在覆盖`FlushAsync()`。如果你看看 thrift 生成的代码:

```
await oprot.WriteMessageBeginAsync(..., cancellationToken);
// Write the message...
await oprot.WriteMessageEndAsync(cancellationToken);
await oprot.Transport.FlushAsync(cancellationToken); 
```

Enter fullscreen mode Exit fullscreen mode

传输的`FlushAsync()`方法在每个消息的结尾被调用。我们用它来处理完整的信息:

1.  包含序列化节俭消息的构造`NetMQMessage`
2.  `MDPClient.Send()`关于零 q
3.  如果有回复，设置`TStreamClientTransport.InputStream`

最后，`Common.MemoryPool.GetStream()`是 [`Microsoft.IO.RecyclableMemoryStreamManager.GetStream()`](http://www.philosophicalgeek.com/2015/02/06/announcing-microsoft-io-recycablememorystream/) 的包装。

## 协议

该协议并不特别有趣，它继承了 [`TBinaryProtocol`](https://github.com/apache/thrift/blob/master/lib/netcore/Thrift/Protocols/TBinaryProtocol.cs) 的行为，并包装了`MDPClient`和`ZeroMqTransport`。这里是为了完整性:

```
public class ZeroMqProtocol : TBinaryProtocol
{
    private MDPClient syncClient = null;
    private ZeroMqTransport zmqTransport = null;

    public ZeroMqProtocol(string brokerAddr, byte[] ClientID) : base(null)
    {
        syncClient = new MDPClient(brokerAddr, ClientID);
        zmqTransport = new ZeroMqTransport(syncClient);
        Trans = zmqTransport;
    }

    protected override void Dispose(bool disposing)
    {
        if (zmqTransport != null)
        {
            zmqTransport.Close();
            zmqTransport.Dispose();
            zmqTransport = null;
        }
        if (syncClient != null)
        {
            syncClient.Dispose();
            syncClient = null;
        }
        base.Dispose(disposing);
    }

    public string Channel
    {
        set { zmqTransport.Channel = value; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 脚注

这可能是也可能不是正确的做法。这是我们早期的一段代码，当时我们还在研究 ZeroMQ 和 Thrift。

无论如何，它极大地简化了我们的架构，因为只有一个连接来源:ZeroMQ。