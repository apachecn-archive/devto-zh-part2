# 发布了我的第一个 NPM 软件包，它确实有所作为

> 原文：<https://dev.to/brsjsk/published-my-first-npm-package-that-actually-does-something-3l9n>

昨天早上我玩了一些个人项目，发现自己不得不添加一个简单的加载程序。没什么复杂的，但我只是想用不同的方式。

我可以创建一个包，以后可以重用吗？当然可以。

有了一个想法，我可以这样做，所以只是打开 StackBlitz，并开始玩它。它工作得很好，做了它应该做的事情。

# 什么事？

只是一个简单的 JavaScript 库，用于显示和隐藏加载器。

# 为什么？

为什么不呢？制作起来很有趣，我可以随时使用它。

# 用法

这很简单。你初始化一个加载器，并把你想要显示的元素的 id 和加载器的类型作为参数发送。

```
let loader = new Loader('app', 'standard');

// show loader
loader.show();

// hide loader
loader.hide(); 
```

目前，有 4 种不同的加载程序，但我可能会添加更多。
你可以在 [github 回购](https://github.com/BrsJsk/brsjs-loader)查看。

没有什么复杂或新的东西，但制作和玩它很有趣。