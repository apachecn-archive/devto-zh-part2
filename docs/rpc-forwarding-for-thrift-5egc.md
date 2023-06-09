# 节约 RPC 转发

> 原文：<https://dev.to/jeikabu/rpc-forwarding-for-thrift-5egc>

我前段时间开始写这个帖子。我相信我们可以改进当前的实现，这将是一个有趣的阅读。相反，我们可能会放弃 Apache Thrift，这样改进的版本将永远不会出现。对于某些人来说，这可能仍然是有价值的(或者至少是有趣的)，所以这是当前的版本。

我们需要以消息不可知的方式转发 Thrift RPC 调用。出于各种原因，这很有用:

*   桥接不同的传输/协议(例如 HTTP 命名管道)
*   消息路由/过滤/记录或其他处理

## 消息处理器

通过从 [`ITAsyncProcessor`](https://github.com/apache/thrift/blob/master/lib/netcore/Thrift/ITAsyncProcessor.cs) :
中导出来创建节俭消息处理器

```
public class ForwardingProcessor : ITAsyncProcessor
{
    public ForwardingProcessor(TProtocol prot)
        : this(prot, prot)
    { }

    public ForwardingProcessor(TProtocol fromDest, TProtocol toDest)
    {
        FromDest = fromDest;
        ToDest = toDest;
    }

    public Task<bool> ProcessAsync(TProtocol fromSrc, TProtocol toSrc)
    {
        return ProcessAsync(fromSrc, toSrc, CancellationToken.None);
    }

    TProtocol FromDest; // Read from destination
    TProtocol ToDest; // Write to destination
} 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
void Register(TMultiplexedProcessor multiplexor)
{
    var protocol = new ZeroMqProtocol(brokerAddress);
    protocol.Channel = serviceName;
    var processor = new ForwardingProcessor(protocol);
    multiplexor.RegisterProcessor("SER_" + serviceName, processor);
} 
```

Enter fullscreen mode Exit fullscreen mode

举例来说， [TMultiplexedProcessor](https://github.com/apache/thrift/blob/master/lib/netcore/Thrift/TMultiplexedProcessor.cs) 可能与我们正在使用的[ASP.NET 核心](//./migrating-to-aspnet-core-w-apache-thrift-45f6)是同一处理器。`ZeroMqProtocol`是我们的 [ZeroMQ 节俭运输](//./apache-thrift-over-zeromq-66e)。这将起到桥接 HTTP 和 ZeroMQ 的作用。

产生的消息流是:

1.  HTTP 客户端连接，[发送类似`SER_L10NSERVICE:GetCurrentLanguage`](//./rust-w-apache-thrift-h8a) 的消息
2.  ASP.NET 核心将其分派给`THttpHandler`中间件(使用`TJSONProtocol`的`TMultiplexedProcessor`实例)
3.  它被映射到调用了`ProcessAsync()`的`ForwardingProcessor`实例
4.  RPC 请求被写入`ZeroMqProtocol`

## 长道

我们的第一个(也是当前的)实现将来自一个`TProtocol`的消息反序列化，并将其序列化到另一个:

```
public async Task<bool> ProcessAsync(TProtocol fromSrc, TProtocol toSrc, CancellationToken token)
{
    try
    {
        TMessage msg = await fromSrc.ReadMessageBeginAsync();
        // Save sequence id to make sure our response contains the same as the request
        var seqId = msg.SeqID;

        if (!IsForwarded())
        {
            return false;
        }

        await ToDest.WriteMessageBeginAsync(msg);

        // Forward RPC args
        await forwardStruct(fromSrc, ToDest, token);
        await fromSrc.ReadMessageEndAsync();
        await ToDest.WriteMessageEndAsync();
        await ToDest.Transport.FlushAsync();

        // Return RPC result
        var resp = await FromDest.ReadMessageBeginAsync();
        resp.SeqID = seqId;
        await toSrc.WriteMessageBeginAsync(resp);
        if (msg.Type == TMessageType.Exception)
        {
            var x = await TApplicationException.ReadAsync(FromDest, token);
            await x.WriteAsync(toSrc, token);
        }
        else
        {
            await forwardStruct(FromDest, toSrc, token);
        }

        await FromDest.ReadMessageEndAsync();
        await toSrc.WriteMessageEndAsync();
        await toSrc.Transport.FlushAsync();
    }
    catch (IOException)
    {
        return false;
    }
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

`IsForwarded()`函数是我们可能想要做的过滤逻辑的占位符。这目前被硬编码为返回`true`，但是如果/当我们开始使用它时，早期返回可能需要使用`TProtocolUtil.SkipAsync()`和/或写某种类型的响应。

### 正向结构

```
/// <summary>
/// Reads <see cref="TStruct"/> from <paramref name="iprot"/> and writes to <paramref name="oprot"/>.
/// </summary>
/// <param name="iprot"></param>
/// <param name="oprot"></param>
async Task forwardStruct(TProtocol iprot, TProtocol oprot, CancellationToken token)
{
    iprot.IncrementRecursionDepth();
    oprot.IncrementRecursionDepth();

    try
    {
        TField field;
        var tstruct = await iprot.ReadStructBeginAsync();
        await oprot.WriteStructBeginAsync(tstruct);

        while (true)
        {
            field = await iprot.ReadFieldBeginAsync();

            if (field.Type == TType.Stop)
            {
                // NB: With Stop field don't WriteFieldBegin() or End()
                await oprot.WriteFieldStopAsync();
                break;
            }

            await oprot.WriteFieldBeginAsync(field);
            await forwardElement(field.Type, iprot, oprot, token);
            await Task.WhenAll(
                iprot.ReadFieldEndAsync(),
                oprot.WriteFieldEndAsync()
                );
        }
        await Task.WhenAll(
            iprot.ReadStructEndAsync(),
            oprot.WriteStructEndAsync()
            );
    }
    finally
    {
        iprot.DecrementRecursionDepth();
        oprot.DecrementRecursionDepth();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，这段代码在很大程度上基于生成的源代码。

避开`ReadFieldEnd()`以及`WriteFieldBegin|End()`的`TType.Stop`的处理。

`await Task.WhenAll(task, ...)`使用 [`WhenAll()`](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenall) 来重叠独立操作。

### 转发类型

```
async Task forwardElement(TType elementType, TProtocol iprot, TProtocol oprot, CancellationToken token)
{
    switch (elementType)
    {
        case TType.Bool:
            var vBool = await iprot.ReadBoolAsync();
            await oprot.WriteBoolAsync(vBool);
            break;
        case TType.Byte:
            var vByte = await iprot.ReadByteAsync();
            await oprot.WriteByteAsync(vByte);
            break;
        case TType.Double:
            var vDouble = await iprot.ReadDoubleAsync();
            await oprot.WriteDoubleAsync(vDouble);
            break;
        case TType.I16:
            var vI16 = await iprot.ReadI16Async();
            await oprot.WriteI16Async(vI16);
            break;
        case TType.I32:
            var vI32 = await iprot.ReadI32Async();
            await oprot.WriteI32Async(vI32);
            break;
        case TType.I64:
            var vI64 = await iprot.ReadI64Async();
            await oprot.WriteI64Async(vI64);
            break;
        case TType.List:
            var list = await iprot.ReadListBeginAsync();
            await oprot.WriteListBeginAsync(list);
            for (int i = 0; i < list.Count; ++i)
            {
                await forwardElement(list.ElementType, iprot, oprot, token);
            }
            await Task.WhenAll(
                iprot.ReadListEndAsync(),
                oprot.WriteListEndAsync()
                );
            break;
        case TType.Map:
            var map = await iprot.ReadMapBeginAsync();
            await oprot.WriteMapBeginAsync(map);
            for (int i = 0; i < map.Count; ++i)
            {
                await forwardElement(map.KeyType, iprot, oprot, token);
                await forwardElement(map.ValueType, iprot, oprot, token);
            }
            await Task.WhenAll(
                iprot.ReadMapEndAsync(),
                oprot.WriteMapEndAsync()
                );
            break;
        case TType.Set:
            var set = await iprot.ReadSetBeginAsync();
            await oprot.WriteSetBeginAsync(set);
            for (int i = 0; i < set.Count; ++i)
            {
                await forwardElement(set.ElementType, iprot, oprot, token);
            }
            await Task.WhenAll(
                iprot.ReadSetEndAsync(),
                oprot.WriteSetEndAsync()
                );
            break;
        case TType.String:
            var vString = await iprot.ReadStringAsync(token);
            await oprot.WriteStringAsync(vString, token);
            break;
        case TType.Struct:
            await forwardStruct(iprot, oprot, token);
            break;
        case TType.Void:
            throw new System.NotImplementedException();
        default:
            // Unexpected type. More robust to skip than throw exception
            await TProtocolUtil.SkipAsync(iprot, elementType, token);
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一个大 ol' `switch`语句 1:1 映射`TType`到`TProtocol.(Read|Write)*Async()`方法。

`TType.List`、`TType.Map`、`TType.Set`都差不多。开始/结束读/写对，在它们之间，我们为集合的每个元素递归到`forwardElement()`中。

`TType.Struct`递归到`forwardStruct()`来处理嵌套结构。

抛出异常，因为我怀疑它不会发生:

*   我们没有一个`void`结构成员的例子
*   [`TProtocolUtil.SkipAsync()`](https://github.com/apache/thrift/blob/af7ecd6a2b15efe5c6b742cf4a9ccb31bcc1f362/lib/netcore/Thrift/Protocols/Utilities/TProtocolUtil.cs#L27) 实现不处理`TType.Void`

`default`由于我们处理所有的`TType`值，目前无法发生案例。但是我们`TProtocolUtil.SkipAsync()`以防有人添加类型。

## 短道？

我们已经提到了一个已经做了我们想要做的事情的组件-`TMultiplexedProcessor`；它接收消息，处理服务报头，并将其转发给注册的处理器。

里面 [`TMultiplexedProcessor.ProcessAsync()`](https://github.com/apache/thrift/blob/af7ecd6a2b15efe5c6b742cf4a9ccb31bcc1f362/lib/netcore/Thrift/TMultiplexedProcessor.cs#L41) :

```
// Create a new TMessage, removing the service name
var newMessage = new TMessage(
    message.Name.Substring(serviceName.Length + TMultiplexedProtocol.Separator.Length),
    message.Type,
    message.SeqID);

// Dispatch processing to the stored processor
return
    await
        actualProcessor.ProcessAsync(new StoredMessageProtocol(iprot, newMessage), oprot,
            cancellationToken); 
```

Enter fullscreen mode Exit fullscreen mode

其中 [`StoredMessageProtocol`为](https://github.com/apache/thrift/blob/af7ecd6a2b15efe5c6b742cf4a9ccb31bcc1f362/lib/netcore/Thrift/TMultiplexedProcessor.cs#L122) :

```
private class StoredMessageProtocol : TProtocolDecorator
{
    readonly TMessage _msgBegin;

    public StoredMessageProtocol(TProtocol protocol, TMessage messageBegin)
        : base(protocol)
    {
        _msgBegin = messageBegin;
    }

    public override async Task<TMessage> ReadMessageBeginAsync(CancellationToken cancellationToken)
    {
        if (cancellationToken.IsCancellationRequested)
        {
            return await Task.FromCanceled<TMessage>(cancellationToken);
        }

        return _msgBegin;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们怀疑有一种方法可以利用这一点来实现`ForwardingProcessor.ProcessAsync()`,而不需要先反序列化再重新序列化每条消息。问题是作为一个`TProtocolDecorator`，它定义了一个静态的管道，但是它并没有主动地将消息拉过来，这样它们就可以在其他地方被写出来。

可悲的是，我们只能做到这一步了。