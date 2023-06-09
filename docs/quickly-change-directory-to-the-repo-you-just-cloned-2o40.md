# 快速将目录切换到您刚刚克隆的 Repo

> 原文：<https://dev.to/hoelzro/quickly-change-directory-to-the-repo-you-just-cloned-2o40>

*最初发表于[hoelz . ro](https://hoelz.ro/blog/quickly-change-directory-to-the-repo-you-just-cloned)T3】*

我的 shell 使用中的一个常见模式是这样的:

```
 $ mkdir a-directory-name
  $ cd !$ 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不熟悉它的人来说，`!$`是上一个命令的最后一个参数的 Bash 历史扩展——所以我上面的例子创建了一个目录，然后将 cd 放入其中。然而，当使用`git clone` :
的单参数形式时，这个技巧就不适用了

```
 $ git clone hoelzro:linotify
  $ cd !$
  bash: cd: hoelzro:linotify: No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

所以我扩充了 Bash 的`cd`函数，使其在这个上下文中工作:

```
cd() {
    if [[ $1 =~ ^hoelzro: && ! -d $1 ]]; then
        cd ${1/hoelzro:/}
    elif [[ $1 =~ github:.*/ && ! -d $1 ]]; then
        cd ${1/github:*\//}
    else
        builtin cd "$@"
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

我后来转换成了 Zsh，所以我也创建了一个 Zsh 版本:

```
function cd {
    local previous_command

    previous_command=$(fc -nl -1 -1)

    if [[ $previous_command =~ ^git && $previous_command =~ clone ]]; then
        if [[ ! -d $1 && $1 =~ (hoelzro|github): ]]; then
            local destination

            destination=$1
            destination=${destination#(github:*/|hoelzro:)}
            destination=${destination%[.git]}

            builtin cd "$destination"
            return
        fi
    fi
    builtin cd "$@"
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在当我在一个`git clone`之后`cd !$`的时候，我的 shell 进入了我刚刚克隆的库的副本！这两种方式都依赖于使用[远程快捷键](https://hoelz.ro/blog/adding-remote-shortcuts-to-git)，但是因为我几乎只使用这些快捷键，所以这对我很有用！