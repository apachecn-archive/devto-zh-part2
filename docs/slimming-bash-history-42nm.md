# 蒂尔:减肥狂欢的历史

> 原文：<https://dev.to/jonasbn/slimming-bash-history-42nm>

# 瘦身痛击历史

如果您想让 shell 中的历史记录更有价值，忽略所有的基本命令，看看如何设置环境变量:`HISTIGNORE`。

这是基于 Kenneth Geisshirt 在 SlideShare 上的幻灯片中的例子，加上我自己的补充。

```
$ export HISTIGNORE="pwd:ls:ls -l:cd:clear" 
```

Enter fullscreen mode Exit fullscreen mode

[来源:SlideShare](http://www.slideshare.net/geisshirt/unleash-your-inner-console-cowboy-47244006)

最初发布于[我的 TIL 收藏](https://jonasbn.github.io/til/)