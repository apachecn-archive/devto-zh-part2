# 基本 Git 配置

> 原文：<https://dev.to/lobo_tuerto/basic-git-configuration-5545>

* * *

你可以在 [lobotuerto 的 notes - Basic Git configuration 查看这篇文章的最新更新版本。](https://lobotuerto.com/blog/basic-git-configuration/)

* * *

# 简介

如果你是一个 Manjaro Linux 用户，那么你的机器中已经有了 T2 Git。

如果你是一个 **Ubuntu** 用户，用
安装它

```
sudo apt install git 
```

Enter fullscreen mode Exit fullscreen mode

你可以用这个来检查它的工作情况:

```
git --version
# git version 2.17.1 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续一些基本的设置，这样我们就能够初始化新项目并提交。

## 用户和邮箱

**Git** 将使用这些数据来*标记*您创建的任何提交:

```
git config --global user.name "Your name goes here"
git config --global user.email "your@email.goes.here" 
```

Enter fullscreen mode Exit fullscreen mode

## 多彩 CLI

做`git status`和`git diff` :
时有彩色输出

```
git config --global color.ui "auto" 
```

Enter fullscreen mode Exit fullscreen mode

## CPU 线程

启用 CPU 线程的自动检测以用于打包存储库:

```
git config --global pack.threads "0" 
```

Enter fullscreen mode Exit fullscreen mode

## 有用的别名

将此添加到您的`~/.gitconfig` :

```
[alias]
    l = log --oneline --decorate --graph

    ll = log --graph --abbrev-commit --decorate --date=relative \
    --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) \
    %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)'

    lll = log --graph --abbrev-commit --decorate --date=relative \
    --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) \
    %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' \
    --branches

    co = checkout
    ci = commit
    man = help h = help a = add
    f = fetch
    d = diff
    dc = diff --cached
    dt = difftool
    dtc = difftool --cached
    ds = diff --stat
    dsc = diff --stat --cached
    s = status --short --branch
    b = branch

[credential]
    helper = cache

[diff]
    algorithm = patience 
```

Enter fullscreen mode Exit fullscreen mode

## 全球。吉蒂尔

让我们定义一个全局**。gitignore** 文件:

```
git config --global core.excludesfile ~/.gitignore_global 
```

Enter fullscreen mode Exit fullscreen mode

让我们用它来忽略 **Visual Studio 代码**项目文件以及由 **ElixirLS** 插件
生成的文件

```
echo ".vscode/" >> ~/.gitignore_global
echo ".elixir_ls/" >> ~/.gitignore_global 
```

Enter fullscreen mode Exit fullscreen mode

## 处理行尾

### Linux / Mac

如果你是 **Linux / Mac** 用户:

```
git config --global core.autocrlf input
git config --global core.safecrlf true 
```

Enter fullscreen mode Exit fullscreen mode

### 窗口

如果你是 **Windows** 用户:

```
git config --global core.autocrlf true git config --global core.safecrlf true 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

## 基础知识

*   [有 15 分钟想学 Git？](https://try.github.com/)
*   [Git 基础知识指南(教程)](http://gitimmersion.com/)
*   斯科特·沙孔(libro)的 Pro Git
*   [可视化 Git 引用](https://marklodato.github.com/visual-git-guide/index-en.html)
*   [Git 快速参考](http://jonas.nitro.dk/git/quick-reference.html)
*   [Git for everyone](http://www.kernel.org/pub/software/scm/git/docs/gittutorial.html)

## 高级用例

*   [如何用 Gitolite 建立你自己的 Git 库](https://lobotuerto.com/blog/how-to-setup-your-own-private-git-repositories-with-gitolite/)
*   [如何用 Git 设置 Hugo 网站自动生成和部署](https://lobotuerto.com/blog/how-to-setup-automatic-hugo-website-generation-and-deployment-with-git/)

## 托管服务

*   [GitHub](https://github.com/)
*   [吉托利特](https://github.com/sitaramc/gitolite)(自托管)
*   [Bitbucket](https://bitbucket.org/)
*   [GitLab](https://about.gitlab.com/) (可以自托管)