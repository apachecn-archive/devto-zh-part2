# 从终端快速谷歌搜索

> 原文：<https://dev.to/mickmister/quick-google-search-from-terminal-4jac>

注意:这只适用于 MacOS

很多时候，当我在做一个项目时，我需要在网上搜索一些东西来解决问题。我一般用谷歌创造的搜索引擎，叫 Google。当我需要搜索一些东西时，我总是不得不停下手头的工作，切换到浏览器来输入我的搜索。这可能看起来没什么大不了的，因为它不需要那么多时间，但实际上这是一个上下文切换。你搜寻的意图有一个短暂的中断。

我希望消除这个障碍，并且能够在不离开我的开发环境的情况下进行搜索。这可以通过使用 MacOS 上的`open`命令和 Linux 上的`chromium-browser`命令来完成。

将这个脚本放在您的路径中，名称为“gsearch”:

~/scripts/gsearch

```
 #!/bin/sh

# If using mac
open "https://google.com/search?q=$*"

# If using linux and chromium
chromium-browser "https://google.com/search?q=$*"

# Now you can enter a search query like this:
gsearch please help google 
```

[![hey](img/e3ce1f1fcccc5a68ba8b9051c9f604c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uet0NtXB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qo5vfi5nai81ctzyfx9a.png)

一个浏览器标签将会出现，显示谷歌搜索“请帮助谷歌”的结果。这才是好的 DX。