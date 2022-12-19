# 一种简单统一的通知发送方式

> 原文：<https://dev.to/liiight/an-easy-and-unified-way-to-send-notifications-1eaj>

当我在做某个项目时(令人惊奇的 [flexget](https://www.flexget.com) ),我需要创建一类通知插件，使用户能够向他们选择的通知提供者发送推送通知。很快就发现这不是一个小任务，因为这意味着深入每个提供商的 API 并尝试实现它。

在这个过程中，您会遇到令人困惑的文档、丢失的文档、不规则的响应模式、不同的响应格式、不同的交互规则等等。一些提供商提供了他们自己的 SDK，但是这意味着在一个已经有很多依赖项的项目中增加了更多的依赖项，更不用说不是所有的依赖项都得到了很好的维护，甚至根本没有维护。一定有更好的方法，对吗？

事实证明没有，或者至少我找不到，所以我决定创建[通知程序](https://www.github.com/liiight/notifiers)，一种与许多通知提供者交互的通用方式。

通过创建一个统一的接口，我能够提供一种简单的方法来与任何实现的通知提供者进行交互，而不管我前面提到的问题。

## 怎么做到的

通知提供者 API(以及任何其他相关的 API)有两个共享的东西。它们接受参数，其中一些是必需的，并返回响应。

为了一般性地实现第一部分，我决定通过 JSON 模式来表示每个提供者 API，这是非常流行的，并且不是 python 特有的。
例如， [Simplepush](https://simplepush.io/) API 对通知的关联 JSON 模式如下:

```
{  "type":  "object",  "properties":  {  "key":  {  "type":  "string",  "title":  "your user key"  },  "message":  {  "type":  "string",  "title":  "your message"  },  "title":  {  "type":  "string",  "title":  "message title"  },  "event":  {  "type":  "string",  "title":  "Event ID"  }  },  "additionalProperties":  false,  "required":  [  "key",  "message"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

显然，其他通知提供者模式看起来比这个模式复杂得多，但是这一点很清楚。很有可能像这样表示一个 API 模式。

下一步是发送通知，并一般地处理响应和/或错误。这几乎完全是通过 HTTP 请求来完成的，这当然意味着使用 [Requests](http://docs.python-requests.org/en/master/) 模块。

这是需要最彻底研究的部分之一，因为每个提供者选择不同的方式来实现他们的 API。一些提供者想要一个 *GET* 请求，大多数更喜欢一个 *POST* 请求，一些使用 URL 编码的内容类型，一个更喜欢 JSON body，一些想要授权通过头部，一些通过 URL 参数等等。

发送请求后，响应被解析为内部响应对象并返回给用户。就是这样，最简单的发送通知的方式！

源代码:[https://github.com/liiight/notifiers](https://github.com/liiight/notifiers)

# 通告程序/技术