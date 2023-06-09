# 去玩壁球吧

> 原文：<https://dev.to/kyleparisi/git-squash-via-sourcetree-2j4>

有一些关于如何/为什么打壁球的非常详细的帖子。尤其是在 cli 中。下面是如何用 sourcetree 做同样的事情。

我是一个 alpine 包的维护者，alpine 包系统的维护者都是坚持者。每次我提交更新，他们都要求我取消提交。这让我很困惑，因为 GitHub 允许你在拉请求时自动这么做。嗯，我想我应该安抚维护者，并学习如何做。

[![Sourcetree](img/85a0ba5c3d4b41972ca9ca27faf7cfc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c-8ErDhR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/ColorlessSeveralAmericanbadger-size_restricted.gif) 
[高清版](https://gfycat.com/gifs/detail/ColorlessSeveralAmericanbadger)

我花了很长时间才意识到要粉碎已经被推送的提交，你需要强制推送。Sourcetree 隐藏了强制推送的能力，我没意见。

```
git push --force 
```

Enter fullscreen mode Exit fullscreen mode

然后在那之后，分支会像这样。

[![After git push --force](img/c763c5790f83a286dd7fdee8d7fa17af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J32OLplt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rvudlahoilhpez6vcj4y.png)

这是你将在你的分支上使用的基本概念。Sourcetree 使流程更加可视化。