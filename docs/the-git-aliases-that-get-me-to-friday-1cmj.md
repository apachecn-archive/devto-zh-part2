# 让我去星期五的饭桶化名

> 原文：<https://dev.to/megamattmiller/the-git-aliases-that-get-me-to-friday-1cmj>

# 把我带到星期五的 Git 别名

[![alt text](../Images/d0cec819951f1e0ae8dae0fef45f5398.png "Git Logo")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k_d3U9ss--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j7to6mqn4b52h31fd9tf.png)

在从 SVN 转到 Git 的过去几年里，我获得了几个别名，它们帮助我简化了工作流程，提高了工作效率。毕竟，捷径的全部意义在于让你的生活更轻松，对吗？我想和你分享我的化名，不管它们有多没用。

## 分支

```
co = checkout # Shortcut for checkout.
nb = checkout -b # Create a new branch and check it out.
bl = branch -l # List all local branches.
br = branch -r # List all remote branches.
blr = branch -a # Show local and remote branches.
bd = branch -d # Politely ask Git to delete a local branch.
bdf = branch -D # Sternly ask Git to delete a local branch. 
```

Enter fullscreen mode Exit fullscreen mode

## 抓取/同步/合并

```
fp = fetch -p # Fetch and prune.
sync = !git pull && git push # Pull then push current branch.
mm = !git fetch -p && git merge origin/master #Merge remote master into the current branch. 
```

Enter fullscreen mode Exit fullscreen mode

## 提交

```
cm = commit # Shortcut for commit.
cma = commit -a # Commit all tracked.
cmam = commit -a -m # Commit all tracked with message to follow.
runAway = reset --hard # For when you just want it all to go away.
forgetAbout = rm --cached # Make Git forget about a tracked file. 
```

Enter fullscreen mode Exit fullscreen mode

## 实用程序

```
alias = config --get-regexp ^alias\\. # List all aliases.
ec = config --global -e # Open .gitconfig in your default editor. 
```

Enter fullscreen mode Exit fullscreen mode

我有所有这些，如果你想在你的 Git 配置中`[include]`它的话，也许更多，当你在一个便利的 [GitHub repo](https://github.com/MegaMattMiller/gitAliases) 中阅读这个的时候。

我希望这些化名对你和我一样有用。

如果你有自己喜欢的别名，请在下面的评论中留下。