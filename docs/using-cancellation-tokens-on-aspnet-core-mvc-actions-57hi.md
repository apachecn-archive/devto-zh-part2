# 在 ASP.NET 核心 MVC 操作中使用取消令牌

> 原文：<https://dev.to/joaofbantunes/using-cancellation-tokens-on-aspnet-core-mvc-actions-57hi>

今天我回来写一个小帖子，关于一个已经有一些帖子的主题，但是我认为还不够广泛，它没有被尽可能多地使用，也许应该使用 ASP.NET 核心 MVC 动作的取消令牌。

如果你想跟随代码，这里是。如果你喜欢看视频走查，跳到文章的结尾。

## 一个场景

假设我们创建了一个 Web API 来支持我们的前端。我们有一个带有自动完成功能的搜索框，有通常的反跳逻辑来避免每次击键时的请求，但即使有了这个逻辑，有时用户需要更长的时间来完成思路。当这种情况发生时，客户端应用程序会发出一个新的请求，并忽略以前的请求。在服务器端，如果我们不希望发生这种情况，应用程序将只是完成请求的操作并发出响应，就像什么也没发生一样，白白完成了一个工作负载，滥用了服务器的资源。

## 一解

幸运的是，这个问题有一个简单愚蠢的解决方案。在我们控制器的动作方法上，我们可以接收一个`CancellationToken`实例，并在我们的异步方法中传递它。当客户端取消一个请求时，这个令牌将被发出信号，我们的方法将得到通知，并可以相应地采取行动。

## 点样

对于示例，我做了一些比我上面谈到的自动完成场景更简单的东西，但是我认为这已经足够了。

```
[Route("thing")]
public async Task<IActionResult> GetAThingAsync(CancellationToken ct)
{
    try
    {
        await _httpClient.GetAsync("http://httpstat.us/204?sleep=5000", ct);
        _logger.LogInformation("Task completed!");
    }
    catch (TaskCanceledException)
    {
        _logger.LogInformation("Task canceled!");

    }
    return NoContent();
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个愚蠢的简单动作，没有客户端提供的参数，只有一个`CancellationToken`。这个`CancellationToken`是由框架注入的，并且将被框架通知取消。在这种情况下，当客户端取消请求时，它会发出信号。在演示代码中，我只是调用了一个外部端点，将花费 5 秒钟完成，将令牌传递给`GetAsync`方法。我在 try catch 中完成了这一切，并期待一个`TaskCanceledException`，因为它是在操作由于取消令牌被发出信号而被取消时抛出的。

下面我添加了一个 gif 图片，快速展示了一个被取消的请求。

[![Cancellation tokens in action](../Images/81c524aac64f40c89f491a76a9297aa4.png)T2】](https://thepracticaldev.s3.amazonaws.com/i/ref5g66uchnqj78zu2l1.gif)

这里是另一个简单的例子:

```
[Route("anotherthing")]
public async Task<IActionResult> GetAnotherThingAsync(CancellationToken ct)
{
    try
    {
        for(var i = 0; i < 5; ++i)
        {
            ct.ThrowIfCancellationRequested();
            //do stuff...
            await Task.Delay(1000, ct);
        }
        _logger.LogInformation("Process completed!");
    }
    catch (Exception ex) when (ex is TaskCanceledException || ex is OperationCanceledException)
    {
        _logger.LogInformation("Process canceled!");

    }
    return NoContent();
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，除了将`CancellationToken`传递给其他异步方法——在这种情况下是`Task.Delay`,但是取消的结果类似于`HttpClient.GetAsync`——我正在检查令牌，看它是否被通知取消。因此，假设`Task.Delay`方法不接受`CancellationToken`作为参数，在下一次循环迭代中，代码将检查取消请求并抛出异常(在本例中为`OperationCanceledException`)。

这在我们不做异步工作的场景中可能是有用的，尽管这需要一段时间来完成，并且会受益于在过程中的可取消性。

## 视频演练

如果你更喜欢视频演示而不是阅读，请便:)
[https://www.youtube.com/embed/KLAsOGyv9ZE](https://www.youtube.com/embed/KLAsOGyv9ZE)
希望这有用。
感谢你的阅读/观看，cyaz！

PS:原贴[此处](https://blog.codingmilitia.com/2018/04/21/using-cancellation-tokens-on-aspnetcore-mvc-actions)