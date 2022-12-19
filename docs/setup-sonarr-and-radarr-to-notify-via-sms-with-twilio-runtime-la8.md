# 设置 Sonarr 和 Radarr，以便在 Twilio 运行时通过 SMS 进行通知

> 原文：<https://dev.to/brandonb927/setup-sonarr-and-radarr-to-notify-via-sms-with-twilio-runtime-la8>

所以，你读了我关于为电影和电视节目设置终极媒体服务器的帖子，你会惊叹于将手动管理如此痛苦的事情自动化是多么美妙的感觉。您可能还想知道在剧集或电影下载时收到通知是什么感觉。我想到了一个可能会引起你兴趣的不太明显的解决方案:下载，等等。通过短信通知。

Sonarr 和 Radarr 有一个被埋没的功能，那就是当剧集或电影被抓取、下载、升级和重命名时，它会通知你。这里有几个选项:电子邮件、twitter DMs、各种推送通知服务和 webhooks 等等。电子邮件通知在紧急情况下也能工作，但是你不能定制内容，而且对于这样的项目来说，一些不同的推送通知成本太高。留给你的是使用 webhooks 来通知你，但是如何开始利用这个特性呢？

## Enter Twilio

Twilio 是一个以开发人员为中心的平台，用于将可编程语音和短信集成到您的产品和项目中。如果你还没有，[创建一个 Twilio 账户](https://www.twilio.com/try-twilio)并购买一个本地电话号码用于可编程短信。如果你已经有了账户和电话号码，那太好了！

### Twilio 运行时功能

在过去的几年中，Twilio 真正扩展了他们的服务，其中之一是提供一个“无服务器”环境，如托管在他们的平台上的[功能](https://www.twilio.com/console/runtime/functions/manage)。

> Twilio Functions 是一个无服务器环境，使像您这样的开发人员能够快速轻松地创建生产级事件驱动的应用程序，这些应用程序可随您的业务而扩展。

Twilio Functions 是一个非常便宜的解决方案，以至于在编写第一个 10K 调用时是免费的，此后每次调用只需 0.0001 美元。如果你在一年内完成了这个项目，我会*非常惊讶*。您确实需要为每条发送的短信支付 0.0075 加元(在撰写本文时)，并为您发送的每个电话号码每月支付 1 加元，但是从长远来看，总成本可以忽略不计。

下面是流程如何工作的总结:

[![How Twilio Runtime Functions work, workflow diagram](../Images/5845d479956d745457b18e265c521ed1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--te28Sx6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/images/Screen_Shot_2017-07-13_at_12.55.39_PM.width-500.png)

*注意*:我不认为 Twilio 运行时被设计成以我提议的方式工作。在未来，这种方法可能不会像这里提到的那样起作用，因此您的里程可能会有所不同。

#### 创建新的运行时函数

此时，请转到[运行时功能](https://www.twilio.com/console/runtime/functions/manage)控制台页面。在那里，创建一个新功能，然后选择`Blank Template`。

在函数编辑器中，设置函数名并设置路径到您想要使用的端点(在这篇文章中，我使用`/notifier`)来执行您的函数。

看一下页面上的代码编辑器，因为这里将编写您的处理程序来处理传入的 webhook 请求并发送文本消息。因为这个处理程序是一个无服务器函数，所以您将使用 Javascript 编写它。

##### 举例

一个非常基本的处理程序根据请求返回一个`200 OK`并注销`context`和`event`对象，如下所示:

```
exports.handler = function(context, event, callback) {
  console.log('Got the message')
  console.log(context)
  console.log(event)

  // Providing neither error or response will result in a 200 OK
  callback()
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置和处理器设置

您可以[配置您的运行时函数](https://www.twilio.com/console/runtime/functions/configure),在`context`对象中包含一个预初始化的 Twilio 客户端、npm 包依赖项，甚至是处理程序可用的环境变量。您可能想看一看[函数执行文档](https://www.twilio.com/docs/runtime/functions/invocation)，以了解关于如何构建您的处理程序的所有细节，以及一些让您入门的示例。

下面是我最终在一个处理程序中处理来自 Sonarr 和 Radarr 的 webhook 事件的结果。你可以把这段代码一字不差地粘贴到你的函数编辑器中，然后点击 Save 来快速开始，但是在你盲目地从网上复制粘贴代码之前，试着思考一下🙂。

```
const get = require('lodash/get')

const EVENT_DOWNLOAD = 'Download'
const EVENT_UPGRADE = 'Upgrade'

const getMovieMessage = event => {
  const {eventType, movie, release} = event

  let message = `Radarr: `
  switch(eventType) {
    case EVENT_DOWNLOAD:
      message += `${movie.title} is ready to watch in ${release.quality}`
      break
    case EVENT_UPGRADE:
      message += `${movie.title} has upgraded to ${release.quality}`
      break
    default:
      message += `Message for '${eventType}' event is not defined`
      break
  }

  return message
}

const getTVShowMessage = event => {
  const {eventType, episodes, series} = event

  let epTitles = []
  episodes.forEach(episode => {
    const {title, seasonNumber, episodeNumber} = episode
    epTitles.push(`${title} (S${seasonNumber}E${episodeNumber})`)
  })

  let message = `Sonarr: `
  switch(eventType) {
    case EVENT_DOWNLOAD:
      message += `${series.title} has episodes '${epTitles.join(', ')}' ready to watch`
      break
    case EVENT_UPGRADE:
      message += `${series.title} have upgraded episodes '${epTitles.join(', ')}' ready to watch`
      break
    default:
      message += `Message for '${eventType}' event is not defined`
      break
  }

  return message
}

exports.handler = function(context, event, callback) {
  console.log('Got the message! Parsing now...')

  const eventType = get(event, 'eventType', null)
  if (eventType === null) {
    // Probably not a request we care about, just 200 OK
    callback()
  }

  const client = context.getTwilioClient()
  let messageBody

  // Check if we're parsing a Radarr or Sonarr event
  const movie = get(event, 'movie', null)
  if (movie !== null) {
    // Radarr event
    messageBody = getMovieMessage(event)
  } else {
    // Sonarr event
    messageBody = getTVShowMessage(event)
  }

  console.log('Parsed and built the message, sending a text.')

  client.messages.create({
    to: process.env.TO_NUM,
    from: process.env.FROM_NUM,
    body: messageBody
  }, function (err, res) {
    console.log("We're done!")
    callback()
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试函数端点

下面是我用来模拟 webhook 攻击我的函数的一些测试命令。当您设置并部署了您的函数后，您可以通过从函数页面复制您的个人运行时 HTTP url 并替换下面示例中的`https://secret-runtime-slug.twil.io/notifier`来测试它:

```
# Single TV show episode
echo '{"episodes":[{"id":123,"episodeNumber":1,"seasonNumber":1,"title":"Test title","qualityVersion":0}],"eventType":"Test","series":{"id":1,"title":"Test Title","path":"C:\\testpath","tvdbId":1234}}' \
  | http POST https://secret-runtime-slug.twil.io/notifier

# Multiple TV show episodes
echo '{"episodes":[{"id":123,"episodeNumber":1,"seasonNumber":1,"title":"Test title","qualityVersion":0},{"id":456,"episodeNumber":9,"seasonNumber":4,"title":"Another test title","qualityVersion":0},{"id":789,"episodeNumber":23,"seasonNumber":10,"title":"Yet another test title","qualityVersion":0}],"eventType":"Test","series":{"id":1,"title":"Test Title","path":"C:\\testpath","tvdbId":1234}}' \
  | http POST https://secret-runtime-slug.twil.io/notifier

# Movie
echo '{"remoteMovie":{"tmdbId":1234,"imdbId":"5678","title":"Test title","year":1970},"release":{"quality":"Test Quality","qualityVersion":1,"releaseGroup":"Test Group","releaseTitle":"Test Title","indexer":"Test Indexer","size":9999999},"eventType":"Test","movie":{"id":1,"title":"Test Title","releaseDate":"1970-01-01","folderPath":"C:\\testpath"}}' \
  | http POST https://secret-runtime-slug.twil.io/notifier 
```

Enter fullscreen mode Exit fullscreen mode

*注*:我用的是 [`httpie`](https://httpie.org) ，这里用的是`http`。这些测试调用使用的有效负载直接来自 Sonarr 和 Radarr 在各自的应用程序 UI 中配置 webhook 通知时发送的数据，因此您可以按原样使用它们，而不是使用它们 UI 中的“测试”按钮。

以下是在“信息”中收到短信时的屏幕截图:

<source>
[![ Messages Twilio SMS conversation](../Images/d1f94807660e7e4b10384c1a6848dadc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ocN1-K95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dc1r9kxqg42ml.cloudfront.net/brandonb.ca/posts/setup-sonarr-radarr-notify-sms-with-twilio/twilio-messages.png)T6】

如你所见，我不仅可以在手机上收到这些通知信息，也可以在 Mac 上收到！这确保了我的至少一个设备会在某个地方收到通知，但至少我会在手机上收到通知。