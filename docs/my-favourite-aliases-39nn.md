# 我最喜欢的别名

> 原文：<https://dev.to/mrtnrdl/my-favourite-aliases-39nn>

受[丹尼尔斯博客](https://danielmiessler.com/blog/some-of-my-favorite-shell-aliases-from-over-the-years/)的启发，我查看了一下我的`alias.zsh`文件，我用它把所有的化名保存在一个地方。

## 别名为所有别名

```
alias aliases="cat ~/.dotfiles/zsh/.zsh/aliases.zsh" 
```

不确定自己有没有化名？不记得您要查找的具体别名？只要`aliases`一下，你就能得到清单。

## 去吧

对于我的 git 工作流，我非常依赖别名来减少必要的输入:

```
alias gc="git commit"
alias gs="git status"
alias gd="git diff"
alias gf="git fetch"
alias gm="git merge"
alias gma="git merge --abort"
alias gr="git rebase"
alias gp="git push"
alias gpf="git push --force-with-lease"
alias gu="git unstage"
alias gg="git graph"
alias gl="git log --pretty --oneline --graph"
alias gA="git add -A"
alias gri="git rebase -i"
alias grc="git rebase --continue"
alias gra="git rebase --abort" 
```

## sbt

目前我大部分时间都在使用 scala 和 sbt，由于这种情况，我为此添加了几个别名。

```
alias sfmt="sbt scalafmt test:scalafmt sbt:scalafmt"
alias sdt="sbt clean dependencyTree"
alias sdc="sbt clean dependencyCheck"
alias sdu="sbt clean dependencyUpdates"
alias ssg="sbt clean scapegoat"
alias sct="sbt clean test" 
```

## 天气

你知道那些你几乎看不到太阳的日子吗？再也不用担心了——有了这个别名，您就可以在`<location>`中了解天气情况了！

```
alias weather="curl http://wttr.in/<location>" 
```

*图片鸣谢[顾凯文](https://unsplash.com/photos/w7ZyuGYNpRQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T3】*