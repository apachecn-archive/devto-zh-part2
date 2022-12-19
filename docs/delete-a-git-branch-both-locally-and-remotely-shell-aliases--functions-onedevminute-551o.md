# 本地和远程删除 Git 分支[Shell 别名+函数] #OneDevMinute

> 原文：<https://dev.to/ahmadawais/delete-a-git-branch-both-locally-and-remotely-shell-aliases--functions-onedevminute-551o>

在本地删除一个 git 分支非常简单，只需执行一个`git branch -D branch_name`就可以了。但是，当您已经将该分支推到远程时，它就变得有点复杂了。你不能删除本地分支，然后做一个`git push`来处理它。相反，你必须做一个`git push origin --delete branch_name`来从遥控器上删除它。

对于一个简单的删除 git 分支的任务来说，所有这些都太多了。这就是为什么，只要将下面的内容复制/粘贴到你的`.zshrc/.bashrc`文件中，你就可以使用`gcm`、`gbn`、`gbdel`命令在瞬间完成所有这些。

```
# FILE: .zshrc/.bashrc

################################################
# 🔥 #OneDevMinute
#
# Daily one minute developer tips.
# Ahmad Awais (https://twitter.com/MrAhmadAwais)
################################################

# Checkout to master
alias gcm="git checkout master"

# Create new branch and checkout.
alias gbn='git checkout -b'

# Remove git branch both locally and remotely.
# Usage: gbdel branch_name
function gbdel {
    # Branch name present?
    if [[ -z "$1" ]]; then echo "\n🤔 Oops… you forgot to provide the branch name"
        echo "👉 E.g. gbdel branch_name\n"
    else
        # Start deleteing.
        echo "\n⏳ Deleting…\n"
        git branch -D "$1" # Local delete.
        git push origin --delete "$1" # Remote delete.
        echo "\n✅ Git branch $1 was deleted from local and remote.\n"
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

*   📺1080p 上[https://Youtu.be/sLif9Ll597s](https://Youtu.be/sLif9Ll597s)
*   👨‍💻代码要点:[https://Ahmda.ws/2xvcyqP](https://Ahmda.ws/2xvcyqP)
*   📨时事通讯+网站即将推出

> P.S .如果你喜欢我的作品，欢迎分享，喜欢。我在 twitter 上相当活跃，你可以在那里找到我讲的无聊笑话，也许[在 Twitter 上关注我](https://twitter.com/MrAhmadAwais)联系和[订阅我的 YouTube 频道→](https://YouTube/AhmadAwais)
> 和平！✌️

如果你没得到什么，请在下面评论🗣️。