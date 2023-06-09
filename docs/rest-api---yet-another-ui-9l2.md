# REST API——又一个 UI

> 原文：<https://dev.to/davidszabo97/rest-api---yet-another-ui-9l2>

我正在开发我的新产品，开发后端是我的任务。
这并不是一个复杂的技术堆栈，我们只准备了 MVP 的几个部分:

```
MongoDB <-> Node.js <-> React Native (Android & iOS) 
```

Enter fullscreen mode Exit fullscreen mode

我已经有一段时间没有做一个不小的项目了。我正在尝试将服务层与“Web 服务(层)”(REST API)解耦，我刚刚意识到了一些事情:

# **REST API 只是一个 UI**

这可能是一个有点愚蠢或大胆的说法，但如果你仔细想想，我认为这是有道理的。当我意识到这一点时，技术栈发生了变化:

```
MongoDB <-> Core (Node.js) <-> REST API (Node.js) <-> React Native (Android & iOS) 
```

Enter fullscreen mode Exit fullscreen mode

所有这些部分都是解耦的。我可能很愚蠢，但这对我来说是一个严肃的认识。

* * *

我只是想与你们分享这些，并想听听你们的想法。我们从未停止学习，希望你能写些东西，让我意识到一些非常重要的事情。