# 用于简易目录导航的 Shell 别名#OneDevMinute

> 原文：<https://dev.to/ahmadawais/shell-aliases-for-easy-directory-navigation-onedevminute-30nm>

> 🔥#OneDevMinute 是我关于一分钟内开发技巧的新系列。祝我好运，让它每天都保持一致。你的支持对我来说很重要。也欢迎对视频的反馈。

曾经有过写`cd..`而不是`cd ..`的打字错误吗——这篇技巧文章不仅解决了这个打字错误，还添加了几个其他的别名来帮助你轻松地浏览你的系统目录。视频中的更多内容…

[![OneDevMinute](img/cb437c70483f5b9bc872def05f14c2fd.png)](https://www.youtube.com/watch?v=x-on-xD3OdE&feature=youtu.be)T3】

```
################################################
# 🔥 #OneDevMinute
#
# Daily one minute developer tips.
# Ahmad Awais (https://twitter.com/MrAhmadAwais)
################################################

# Easier directory navigation.
alias ~="cd ~"
alias .="cd .."
alias ..="cd ../.."
alias ...="cd ../../.."
alias ....="cd ../../../.."
alias cd..="cd .." # Typo addressed. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

> 将这些别名复制粘贴到您的`.zshrc` / `.bashrc`文件的末尾，然后重新加载/重启您的 shell/终端应用程序。

液体错误:内部

> 如果你愿意[📺观看 Youtube 上的 1080 p→](https://www.youtube.com/watch?v=x-on-xD3OdE)
> 这是一个新项目，所以请耐心等待。和平！✌️