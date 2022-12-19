# 如何从一个 Roku 频道启动另一个 Roku 频道

> 原文：<https://dev.to/umitic/how-to-launch-a-roku-channel-from-within-another-roku-channel-b6f>

在 Roku 平台上从一个应用程序启动另一个应用程序听起来很酷，对吗？这听起来也很复杂，尤其是如果你正在遵循 Roku 文档的话。
所以我会尽量简单的解释一下。在本文中，我将只关注负责启动另一个频道的部分代码，如果你想查看整个项目，请点击这里:
【https://github.com/umitic/Roku-Channel-Within】

让我们开始吧:

当你从另一个频道启动 Roku 频道时，你实际上是在向你的 Roku 设备发送 URL 请求。没什么特别的，对吧？为了做到这一点，我们将使用 Roku 外部控制 API。

下面是从任务中启动另一个应用程序的代码:

```
deviceInfo = CreateObject("roDeviceInfo")
deviceIP = deviceInfo.GetIPAddrs().eth1

urlString = "http://" + deviceIP + ":8060/launch/12"     

request = CreateObject("roUrlTransfer")
request.SetUrl(urlString)
request.AsyncPostFromString(urlString) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，让我来解释这段代码:

```
deviceInfo = CreateObject("roDeviceInfo")
deviceIP = deviceInfo.GetIPAddrs().eth1 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建“roDeviceInfo”对象，这样我们就可以通过调用“DeviceInfo”来获取 Roku 的 IP 地址。GetIPAddrs()et h1 "。GetIPAddrs() -返回 roAssociativeArray。关联数组中的每个关键字是网络接口的名称，值是接口的 IP 地址。
通常在关联数组中只有一个接口，这就是为什么我们用“GetIPAddrs()et h1”直接调用它。

```
urlString = "http://" + deviceIP + ":8060/launch/12" 
```

Enter fullscreen mode Exit fullscreen mode

使用这个字符串，我们为启动请求创建 URL。这里重要的是数字“12”！
数字“12”是内部频道 ID，在我的例子中，它是网飞频道的 ID。
你可以从 Roku 频道商店的频道 URL 中获得内部频道 ID。
下面是网飞一的样子:
[https://channelstore.roku.com/details/12/netflix](https://channelstore.roku.com/details/12/netflix)
注意“12”就在“/details/”部分之后。让我们继续前进...

```
request = CreateObject("roUrlTransfer")
request.SetUrl(urlString)
request.AsyncPostFromString(urlString) 
```

Enter fullscreen mode Exit fullscreen mode

这应该很容易理解。我们正在创建“roUrlTransfer”对象，然后我们必须通过传递 urlString 作为参数来设置请求的 URL。
然后我们调用 AsyncPostFromString，使用 urlString 作为参数。

就是这样！您刚刚从您的频道推出了网飞！:-)

还有一件事。在启动网飞频道之前，您必须将它安装在您的设备上。你可以用“普通/非开发者”方式或者你可以修改 urlString 变量来从你的频道安装网飞。:-)
你可以查看 Roku 文档了解更多关于这个的内容:[https://developer . Roku . com/en-GB/docs/developer-program/debugging/external-control-API . MD](https://developer.roku.com/en-gb/docs/developer-program/debugging/external-control-api.md)