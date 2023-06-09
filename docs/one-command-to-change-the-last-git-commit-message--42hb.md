# 更改最后一条 Git 提交消息的命令

> 原文：<https://dev.to/ahmadawais/one-command-to-change-the-last-git-commit-message--42hb>

🔥热点提示:你经常搞砸 git 提交消息吗？我愿意。

[![image](img/61d10a2b1589b9707ab581b90fde2375.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ldCAlxaJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://on.ahmda.ws/0e213760255b/c)

大多数时候，我不得不修改最后一条 git 提交消息。所以，我用它做了一个小的 bash 函数。

```
 # Amend the last commit message.
# Push the changes to remote by force.
# USAGE: gamend "Your New Commit Msg"
function gamend() {
    git commit --amend -m "$@"
    git push --force-with-lease
} 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️避免`--force`除非绝对必要，而且你可以确定没有其他人在同时同步你的项目。
> 
> ℹ️，为什么要用租借的方式来推动力量！如果其他人将更改推送到同一个分支，您可能希望避免破坏这些更改。- force-with-lease 选项是最安全的，因为如果有任何上游更改，它将中止。

✅一次修改 git 提交
🤖放进去。bashrc/。zshrc 等文件
👌分享这个快速提示是一件有趣的事情

> ![unknown tweet media content](img/cf815b636c47a8275fee2ca20eabd1d6.png)![AhmadAwais.js profile image](img/87af3ffd0210294123738700bfe83f7e.png)ahmadawais . js@ mrahmadawais![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)🔥热点提示:你经常搞砸 git 提交消息吗？我愿意。
> 
> 大部分时间我都要修改最后一条 git 提交消息。所以，我用它做了一个小的 bash 函数。
> 
> ✅一口气修改 git 提交
> 🤖放进去。bashrc/。zshrc 等文件
> 👌转发是一件很好玩的事2018 年 9 月 09:56 AM - 14 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1040539814536392709)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1040539814536392709)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1040539814536392709)