# 哦 git 配置！让我们简化一下

> 原文：<https://dev.to/tomerbendavid/git-configurations-2ngi>

## 哦 Git 很复杂！

Git 是一个复杂的系统。要查看检验结果:

1.  [ohshitgit.com](http://ohshitgit.com)
2.  [gitfightrules.com](https://github.com/k88hudson/git-flight-rules)
3.  [git-man-page-generator.net](https://git-man-page-generator.lokaltog.net/)

Prooved. ;)

为了减轻这种影响**，我们将进入`Linus Torvalds`的大脑！**(只有一点点可能变成单个神经元)，而且，在这一部分，我们将着重于配置。我一直认为，要了解一个系统，我需要了解它的配置(或者更好的是它的安装)。

当我到一家新公司时，我首先要弄清楚的是:

1.  我如何安装这个？
2.  我该如何配置？

## 莱纳斯·托沃兹是怎么想的？

> 一切都是一个文件

是的，莱纳斯是这样想的:一切都是一个文件

因为 Linus 喜欢一切都是一个文件，所以您可以将 git 配置作为一个文件来查看。

因此，如果我们设法获得 Linus 在 git 中使用的文件，我们就有可能渗透到 oh-my-complex-git！

## 3 层回退配置

**1。系统-你的操作系统 git 配置- `git config --system`**
**2。Global -您的用户 git config - `git config --global`**
**3。本地-你的库 git 配置- `git config --local`**

```
git config --system # => /etc/gitconfig

git config --global # => ~/.gitconfig or ~/.config/git/config

git config --local # => .git/config 
```

Enter fullscreen mode Exit fullscreen mode

> Git 首先从。git/config-->[回退到] ~/。gitfconfig-->[回退到] /etc/gitconfig

## 电子邮件每回购

因此，如果不同的存储库有不同的电子邮件地址，这将进入`.gitconfig == git config --local`

## 获取全局配置:`git config --list --global`

```
➜  tmp git config --list --global

user.name=Tomer Ben David
user.email=tomer.bendavid@ohohoh.com
core.autocrlf=input
format.pretty=format:%h %Cblue%ad%Creset %ae %Cgreen%s%Creset 
```

Enter fullscreen mode Exit fullscreen mode

那会和`cat ~/.gitconfig`一样吗你觉得呢？

```
➜  tmp cat ~/.gitconfig
[user]
    name = Tomer Ben David
    email = tomer.bendavid@ohohoh.com
[core]
    autocrlf = input
[format]
  pretty = format:%h %Cblue%ad%Creset %ae %Cgreen%s%Creset 
```

Enter fullscreen mode Exit fullscreen mode

是也不是！不同的符号，但大体相同！

## 获取合并后的配置:`git config --list`

合并的配置是所有具有层次结构的配置的组合，让我们在我的机器上看到它；)

```
git config --list

➜  tmp git config --list
core.excludesfile=~/.gitignore
core.legacyheaders=false core.quotepath=false mergetool.keepbackup=true push.default=simple
color.ui=auto
color.interactive=auto
repack.usedeltabaseoffset=true alias.s=status
alias.a=!git add . && git status
alias.au=!git add -u . && git status
alias.aa=!git add . && git add -u . && git status
alias.c=commit
alias.cm=commit -m
alias.ca=commit --amend
alias.ac=!git add . && git commit
alias.acm=!git add . && git commit -m
alias.l=log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %C(white)- %an, %ar%Creset'
alias.ll=log --stat --abbrev-commit
alias.lg=log --color --graph --pretty=format:'%C(bold white)%h%Creset -%C(bold green)%d%Creset %s %C(bold green)(%cr)%Creset %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
alias.llg=log --color --graph --pretty=format:'%C(bold white)%H %d%Creset%n%s%n%+b%C(bold blue)%an <%ae>%Creset %C(bold green)%cr (%ci)' --abbrev-commit
alias.d=diff
alias.master=checkout master
alias.spull=svn rebase
alias.spush=svn dcommit
alias.alias=!git config --list | grep 'alias\.' | sed 's/alias\.\([^=]*\)=\(.*\)/\1\     => \2/' | sort include.path=~/.gitcinclude
include.path=.githubconfig
include.path=.gitcredential
diff.exif.textconv=exif
credential.helper=osxkeychain
user.name=Tomer Ben David
user.email=tomer.bendavid@ohohohoh.com
core.autocrlf=input
format.pretty=format:%h %Cblue%ad%Creset %ae %Cgreen%s%Creset 
```

Enter fullscreen mode Exit fullscreen mode

这就是我机器上的 git 配置，你有它！我希望我没有在那里放任何密码；)如果我做了，请让我知道，代码审查伙计们，这很重要！

## git 配置-本地/全局/系统用户名-获取单个配置

**逐层询问配置**

```
➜  tmp git config --local user.name
fatal: BUG: setup_git_env called without repository # We called from a non-repository folder.
➜  tmp git config --global user.name
Tomer Ben David
➜  tmp git config --system user.name # => nothing in the global config file.
➜  tmp 
```

Enter fullscreen mode Exit fullscreen mode

啊哈！所以它来自全局(用户)配置！

注意，在文件中，它带有`[user]`和名称，git config 返回组合名称`user.name`

如果你需要一个大于 3 的层次结构，那么在这个文件中应该是这样的:

```
[branch "gke"]
    remote = origin
    merge = refs/heads/gke 
```

Enter fullscreen mode Exit fullscreen mode

所以这个应该是`branch.gke.remote`，我们来验证一下这个:

```
➜  .git git:(gke) git config branch.gke.remote # => yes it is branch.gke.remote!
origin 
```

Enter fullscreen mode Exit fullscreen mode

## 用`git config mysection.mykey myvalue`设置新的配置

```
➜  .git git:(gke) git config mysection.mykey myvalue
➜  .git git:(gke) git config mysection.mykey
myvalue 
```

Enter fullscreen mode Exit fullscreen mode

所以我们能够设置它。让我们来看看这个文件:

```
➜  .git git:(gke) cat config
[core]
    repositoryformatversion = 0
    filemode = true bare = false logallrefupdates = true ignorecase = true precomposeunicode = true
[remote "origin"]
    url = https://bitbucket.org/yshenbaum/k8s-course.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "gke"]
    remote = origin
    merge = refs/heads/gke
[mysection] ##### =====> Here it is! see the section in []
    mykey = myvalue 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

你现在知道 git 配置文件的确切位置了，这非常有用，比使用 git 命令更容易理解。重读这篇文章不确定是否能够简化，但它有一定的意义，至少对我们程序员来说是有意义的！最后一点

## 资源

**[Ry 的 Git 教程(书)](http://devatrest.blogspot.com/2018/03/rys-git-tutorial-book-review.html)**