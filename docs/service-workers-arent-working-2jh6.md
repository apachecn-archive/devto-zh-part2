# 服务人员...不起作用

> 原文：<https://dev.to/dragonwocky/service-workers-arent-working-2jh6>

我知道网上到处都是关于服务人员的文章。但是，我卡住了。

我正试着缓存一些文件供离线使用，这是我正在开发的一个小游戏，我原以为它会相对简单。它以前的版本使用`<html manifest="cache.manifest">`来缓存必要的文件，我计划再做一次...直到我做了一点研究，发现它被否决了。

显然，服务工作者是 appcache 的推荐替代者。出于某种原因，我不能让他们工作。我试着按照指示让一个 web 应用程序离线工作，服务人员不在 [MDN web docs](https://developer.mozilla.org/en-US/Apps/Progressive/Offline_Service_workers) 上，但是我总是在控制台上看到`Uncaught (in promise) TypeError: Request failed`。

非常感谢帮助。

查看游戏@[https://car-cricket . glitch . me](https://car-cricket.glitch.me)
查看源代码@[https://glitch.com/edit/#!/car-cricket](https://glitch.com/edit/#!/car-cricket)