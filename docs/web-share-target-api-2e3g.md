# Web 共享目标 API

> 原文：<https://dev.to/paul_kinlan/web-share-target-api-2e3g>

我一直担心，在网络平台上，我们正在制造[个意想不到的孤岛](https://paul.kinlan.me/unintended-silos)，让数据更难进出网站和应用程序，更重要的是，我担心数据只会单向流动:从网络流向应用程序，因为应用程序可以出现在用户希望它们出现在设备上的所有地方。

我很高兴 Chrome 开始在 Share Target API 上工作[，这补充了在](https://paul.kinlan.me/breaking-down-silos-with-share-target-api) [navigator.share](https://paul.kinlan.me/navigator.share) 上的工作。其中`navigator.share`允许您将网站上的信息分享给用户设备上任何可以接收“分享”(意图)的应用程序。Android 术语中的 ACTION_SEND)，网络共享目标让你的网站(或 PWA)说‘我也想玩那个游戏’。

Web 共享目标 API 是您在 Web 应用程序清单中定义的一个小 API。如果你曾经使用过`registerProtocolHandler`,你会发现这并不遥远——你定义了一个 URL 模板，其中有许多变量，当用户调用动作时，这些变量将被替换。

首先，创建一个名为`share_target`的‘object’属性，其中包含一个名为`url_template`的属性，该属性具有当用户选择我们的服务时应该打开的路径。在 Android 上，您可以使用三个替代名称:

*   `{title}` -相当于 navigator.share API 上的`.title`，或者来自 Android 意图的`Intent.EXTRA_SUBJECT`。
*   `{text}` -相当于 navigator.share API 上的`.text`，或者来自 Android 意图的`Intent.EXTRA_TEXT`。
*   `{url}` -等同于 navigator.share API 上的`.url`，或者来自 Android Intent 的原始数据。

你可以通过安装 Twitter 的 PWA 来尝试一下。[推特的清单在](https://mobile.twitter.com/manifest.json)下面:

```
{
    ...
    "name": "Twitter Lite",
    "share_target": {
        "url_template": "compose/tweet?title={title}&text={text}&url={url}"
    },
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在有一些限制:

*   你只能有一个清单，这意味着在 Twitter 的情况下，他们不能有一个“分享到 DM”。
*   已经提出了一些扩展，比如一个名为`navigator.actions`的服务工作者事件，它将在不打开 UI 界面的情况下被触发，但是它们还没有被实现。
*   你只能分享“文本”，这意味着如果你想分享一个 Blob 的数据，你需要用一个 URL 来保存，然后再被分享出去。
*   它只适用于安卓系统
*   您必须安装 PWA，因此您不能通过注册共享目标来进行驱动。
*   作为清单规范的一部分，它还没有被标准化。:/

抛开局限性不谈，这是对 web 平台的一个相当惊人的补充，是打破 web 在主机平台上集成的巨大障碍的开始。

如果你想跟踪这个 API 的更新，请查看 Chrome Status]([https://chromestatus.com/feature/5662315307335680](https://chromestatus.com/feature/5662315307335680))。