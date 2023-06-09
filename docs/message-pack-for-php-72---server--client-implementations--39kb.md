# PHP 7.2 的消息包-服务器和客户端实现

> 原文：<https://dev.to/ddonprogramming/message-pack-for-php-72---server--client-implementations--39kb>

[![messagepack.png](img/cc7b3bdf2baf51f842c35beed8cc3215.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mnn6rDXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hpiynhbhq/image/upload/v1520644966/ip3wvid0edmroovpe5yt.png)

这个包试图利用[消息包](https://msgpack.org/index.html)和【msgpack.php】T2 的力量

这主要是在更新升级到上述 msgpack.php 包的底层代码&时使用[https://github.com/msgpack-rpc/msgpack-rpc-php](https://github.com/msgpack-rpc/msgpack-rpc-php)。

### 由于其服务器&客户端的观点，该存储库有 3 种可能的用例

#### 客户端(我的主要用例)

```
 $client = new Client('localhost', '1985');

 $messages =  $client->call("SyncJob", $job, $payload);

 echo array_pop($messages); 
```

Enter fullscreen mode Exit fullscreen mode

除了明显的`Client`类的`host`和`port`参数，让我们看看调用函数的参数:

*   第一个参数是一个字符串，命名服务器端要调用的函数(见`tests/server.php`)
*   `call`方法是一个变量函数，因此下面的参数被连接到服务器上的一个数组中

#### 服务器(未彻底测试)

```
 try {
      $server = new Server('1985', new App());
      echo 'Server is listening on port 1985...';
      $server->recv();
  } catch (Exception $e) {
      echo $e->getMessage();
  } 
```

Enter fullscreen mode Exit fullscreen mode

完整示例见`tests/server.php`

#### 客户端和服务器端

如果您使用多个服务或者需要来回通信，这将是一个很好的用例

*如果您需要在相同的两个服务之间切换，最好考虑切换到 http/2，其中

```
 $backChannel = new BackChannel();

     $client = new Client('localhost', '1986', $backChannel);    
     $messages =  $client->call("SyncJob", $job, $payload);    
     echo array_pop($messages);

     try {
         $server = new Server('1985', new App(), $backChannel);
         echo 'Server is listening on port 1985...';
         $server->recv();
     } catch (Exception $e) {
         echo $e->getMessage();
     } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，与原始包的情况一样，使用 BackChannel 的组合服务器和客户端方法是有意义的。

### 其他选项

该包提供了必要的接口和特征，以便拥有自己的服务器或客户端增强功能，而无需修改基类，这些基类主要用作引用而不是实现，因为它只针对最基本的用例。

### 简单说说从 Gearman 切换到 MessagePack 的影响

我预计第一个字节的时间会受到影响，但考虑到它们都在低层次上进行通信，基准对字节几乎是相同的。

然而，正如我所料，可用性发生了巨大的变化，这为我的系统解决了一个大问题。

我很想知道你的经历是什么，你是如何使用它的，或者打算如何使用它，请在下面评论。

* * *

最初发布于[utopia . io-奖励开源贡献者](https://utopian.io/utopian-io/@decebal2dac/message-pack-for-php-server-and-client-libraries)。