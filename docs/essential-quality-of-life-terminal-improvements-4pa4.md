# 基本生活质量终端改善

> 原文：<https://dev.to/thejessleigh/essential-quality-of-life-terminal-improvements-4pa4>

如果你和我一样，你会花很多时间在你的终端上。令人尴尬的是，我有很长一段时间没有定制任何东西，因为我基本上很懒，也不想学习如何定制。然而！通过配置来改善你的终端体验是一个更加有效和懒惰的长期解决方案。现在的几分钟让你以后不再头疼。不要像我一样，等上几年才开始这个项目。

我用的是 MacOS 默认的终端 app。我不用`zsh`或`iTerm`或`Upterm`或其他任何东西。我不喜欢。变得花哨似乎需要做很多工作。我们已经确定我很懒。如果你不同意，请在评论中改变我的想法！

## 基础知识

### 什么是`~/.bash_profile`？

`/.bash_profile`和`~/.bashrc`是调用 bash 时执行的脚本。

*   `~/.bash_profile`为登录 shells 执行脚本
*   `~/.bashrc`为非登录交互式 shells 执行脚本

### 什么意思？

登录 shells 是在用户登录系统的情况下运行的。这仍然是一个交互式会话，因为您输入命令，shell 从它的标准输入中读取它们。您可以在任何需要用户权限或者希望 shell 每次都以一致的配置启动的地方使用登录 shell。

非登录交互式 shells 没有连接到其会话的用户。这对于运行不依赖于登录来运行的自动化流程非常有用。

默认情况下，新 OSX 终端会话是登录 shells。我没有每天在 Linux 机器上运行，但是据我所知，Linux 终端模拟器默认使用非登录交互式 shells 启动。

这篇文章展示了一些生活质量的改善，这将使你的终端体验更加流畅。这些变化主要在`~/.bash_profile`中，这意味着它们将在您的登录 shell 中执行，而不是在您的非登录交互 shell 中。

### 如果我已经有了`~/.bashrc`怎么办？

您可能已经在`~/.bashrc`中放入了配置内容，而只是在您的 shell 中放入了`source`。此外，许多人希望将可用性脚本等功能与修饰性和/或趣味性的改进严格分开。没问题！你不必摆脱它。把这一行包含在`~/.bash_profile` :
里就行了

```
source ~/.bashrc 
```

### 我们今天要讨论的其他文件

`~/.gitconfig`
`~/.config/neofetch/config.conf`
`~/.hushlogin`

## 可用性改进

### git 完成

我最大的一个痛点是 git 分支名称缺少制表符补全。

幸运的是，有一个[开源解决方案](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash)可以解决这个问题。

为了利用这一点，你需要将上面链接的原始内容复制到一个本地文件

```
curl https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash -o ~/.git-completion.bash 
```

现在您可以通过包含以下命令在您的`~/.bash_profile`中运行`git-completion.bash`。加入标签完成善良。

```
source ~/.git-completion.bash 
```

### git 提交归属

确保您在`~/.gitconfig`文件中设置了用户级别信息。它将确保提交被正确地属性化，并且你可以设置它并忘记它。

```
[user]
name = mygithubusername
email = my.github.email@example.com 
```

### 有用的别名

有时在一个命令中键入所有的字符实在是太多了。这里有一些简单的别名，可以让你的生活变得更好。

当然，与任何别名一样，将别名更改为对您最有用的名称。

```
# list files
alias la="ls -oa" # show hidden files and format nicely

# change directories
alias ..="cd .."
alias roj="cd ~/projects" # or wherever you store your main project files

# git aliases
alias gdf="git diff"
alias gap="git add -p"
alias gc="git commit -m"
alias gs="git status"
alias gco="git checkout"
alias gul="git pull"
alias gush="git push"
alias gbra="git branch"
alias glog="git log --pretty=format:'%h - %an: %s' --graph" # print out log with hash, author name, status, and include graph info 
```

#### 哦，不，有东西坏了！

还记得我们之前设置的 git 自动完成功能吗？它被这些别名打破了，这是一个巨大的败笔！

解决方法是修改`~/.git-completion.bash`。现在，根据`~/.git-completion.bash`文件本身的注释:

> 这不是一个公共功能；使用风险自担。

因此，我建议打开一个窗口，在那里你不要寻找新的`~/.bash_profile`源，并且如果你测试它们并且它们不工作，你可以安全地恢复你的改变。

有了这个警告，您可以在`~/.git-completion.bash`的底部添加以下内容，让它与您的别名配合得更好。

```
# git complete aliases
__git_complete gdf _git_diff
__git_complete gbra _git_branch
__git_complete gco _git_checkout 
```

### 提示字符串

我喜欢让我的提示字符串相当简单，因为如果我用太多的信息超载它，我最终会忽略它。

```
parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

PS1="\w | \$(parse_git_branch) 👾 "

PS2=" 🔪 " 
```

这里发生了一些事情。我最喜欢的两个主要提示字符串是我的当前位置和活动的 git 分支。没有一个内置函数来查找活动的 git 分支，所以我在上面定义了一个函数来完成这项工作。

下面是实际提示字符串(`PS1`)的组成部分

*   当前目录(`\w`)
*   为便于阅读的分隔符(`|`)
*   git 分支(括号中的内容更直观)`$(parse_git_branch)`
*   外星人表情符号因为我觉得很可爱(👾).

PS1 是您通常会在命令提示符下看到的字符串。PS2 是当您在命令提示符下按 enter 键并触发第二行时将显示的字符串。PS2 的默认值是`>`。

这里有一个我的 PS1 和 PS2 价值观的例子。

<figure>

[![A picture of my terminal reading `grep "something` and several secondary prompt lines with a knife emoji instead of `>`](img/21f237566446a8d2ba6f7515dd8cdbc0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A-xnWb_L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b0raqf1gray5yk7472ca.png)

<figcaption>I like the knife emoji here because I almost never intentionally trigger the secondary prompt string</figcaption>

</figure>

## 整容改进

### `last login`

我不喜欢新的终端窗口以一个`last login`语句开始。最后一次登录是在我最后一次打开新的终端窗口时，这对我来说不是有用的信息。您可以通过创建这个空文件来删除这一行:

```
touch ~/.hushlogin 
```

这应该会立即生效。你不需要寻找任何东西。下次打开新的终端会话时，您应该不会看到登录信息。如果您决定再次需要登录信息，只需删除该文件。

### 提示字符串颜色

唯一比一个干净且信息丰富的提示字符串更好的是一个干净且信息丰富且带有有趣颜色的提示字符串。

我没有在上面向您展示这一点，因为在 bash 中给任何东西添加颜色都是一件痛苦的事情，所以我希望您能够在绞尽脑汁试图获得正确的颜色之前设置好您的函数`PS1`值。

下面是我的`PS1`值*实际上*的样子:

```
PS1="\e[0;35m\w\e[m \e[0;33m|\e[m \e[0;36m\$(parse_git_branch)\e[m 👾 " 
```

<figure>

[![Terminal prompt with current working directory in purple, a yellow vertical pipe, active git branch in cyan, and an alien emoji](img/d57fd5104f930cd1459c09fe75219fa0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UnbJeOB3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4cpb0sdk0qv1j9yabndt.png)

<figcaption>So much visual information packed in with these colors!</figcaption>

</figure>

当您想要将颜色分配给字符串的一部分时，请使用以下模式:

`\e[X;Ym text you want to be in a color \e[m`

*   `\e[`是颜色编码开始的地方
*   `X;Y`是您使用的颜色代码，在大多数情况下是由分号分隔的两个数字
*   让 bash 知道这是颜色代码的结尾，并开始处理您的 stirng
*   `\e[m`表示您想要颜色编码的部分结束

读起来很乱，我知道我永远也不会记住这些。看看这本列出了几种颜色代码的综合指南。

### `Neofetch`

`neofetch`是一个漂亮的工具，它以一种美观且易于访问的方式显示关于您系统的信息。想知道您使用的是哪个版本的操作系统，您的正常运行时间，或者您在登录 shell 会话中碰巧使用了什么字体？`neofetch`拥有所有这些和更多，加上可定制的 ascii 艺术。

这实际上是部分外观改进和部分可用性改进，但我把它放在外观部分，因为它将对您的终端的外观和感觉产生很大影响。

对于 MacOS，您可以`brew install neofetch`。对于其他操作系统，请遵循其[安装指南](https://github.com/dylanaraps/neofetch)。它们支持大量的操作系统，甚至是晦涩难懂的操作系统。

您可以设置自己的 ascii 图片，以便在运行`neofetch`时出现。安装时，它会创建一个包含一个`config.conf`文件的`~/.config/neofetch`目录。进入文件，将`image_source`位置编辑为您想要使用的 ascii 艺术的绝对路径。遵循[指南](https://github.com/dylanaraps/neofetch/wiki/Custom-Ascii-art-file-format)关于如何给你的艺术作品上色

这是我的 neofetch 设置的图片。(请忽略我近乎荒谬的正常运行时间，我应该更频繁地重启)。

<figure>

[![A screenshot of my terminal output from neofetch, including rainbow ascii art of a demon, operating system and kernel info, uptime, etc.](img/e4867816d8dc643411002be0444c52dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6dAKS8fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vhu4e938i5s9zzgc7put.png)

<figcaption>It's almost Halloween, so I have to show off some spoopy ascii</figcaption>

</figure>

我把它设置成每次打开新的终端窗口时运行`neofetch`。你可以简单地通过调用`~/.bash_profile`底部的`neofetch`来实现。

### git colors!

我喜欢 git 设置的很多默认颜色。`git status`红色表示未提交的变更，绿色表示要提交的变更。诸如此类的事情。但是还有其他我不喜欢的颜色设置。我不喜欢`git status`未跟踪的文件和未暂存但已跟踪的文件是相同的颜色。我不喜欢那个`git diff`元信息和新行都是绿色的。

以下是我如何更改我的设置。我真的很喜欢深色或黑色的背景。`bold`帮助我从`diff`的变化中分离出元信息。我非常喜欢当前分支的`reverse`和`bold`，这样我就可以在一长串分支中快速识别它。

```
[color]
  ui = auto
[color "diff"]
  meta = cyan bold
  frag = magenta bold
[color "status"]
  untracked = cyan
[color "branch"]
  current = cyan reverse bold
  local = green
  remote = yellow 
```

以下是这些选项的一些示例

<figure>

[![Git status output with updated color profile](img/c5408419a748d8dada5f5bec2dec3b09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UcTRiJ20--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kn42otpps1qnjtcizvpr.png)

<figcaption>git status with cyan untracked files</figcaption>

</figure>

<figure>

[![Git diff output with updated color profile](img/a7eb7655f49f4f90e28f5d2ca85856eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f1DPfBDp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tiuekgt6cip3ce12rpve.png)

<figcaption>git diff with bold cyan metadata, bold magenta fragment data</figcaption>

</figure>

<figure>

[![Git branch output with updated color profile](img/7736c97bd5ab254c92f0626b3d9608b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LZuAo1pF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a6pn4j5mdchp7g38rvv3.png)

<figcaption>git branch with green for local branches and reverse cyan for current branch</figcaption>

</figure>

## 最终结果

这里是我的`~/.bash_profile`看起来的样子，对于任何想复制它的人来说都在一个地方。我建议你这么做。这些都是很棒的设置。如果有任何问题，请告诉我！

```
. ~/.git-completion.bash

parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

PS1="\e[0;35m\w\e[m \e[0;33m|\e[m \e[0;36m\$(parse_git_branch)\e[m 👾 "

PS2=" 🔪 "

# list files
alias la="ls -oa" # show hidden files and format nicely

# change directories
alias ..="cd .."
alias roj="cd ~/projects" # or wherever you store your main project files

# git aliases
alias gdf="git diff"
alias gap="git add -p"
alias gc="git commit -m"
alias gs="git status"
alias gco="git checkout"
alias gul="git pull"
alias gush="git push"
alias glog="git log --pretty=format:'%h - %an: %s' --graph" # print out log with hash, author name, status, and include graph info

neofetch 
```

下面是我的`~/.gitconfig`

```
[user]
name = mygithubname
email = my.github.email@example.com

[color]
  ui = auto
[color "diff"]
  meta = cyan bold
  frag = magenta bold
[color "status"]
  untracked = cyan
[color "branch"]
  current = cyan reverse bold
  local = green
  remote = yellow 
```

## 附加好玩的东西

看看开发者社区成员过去分享的终端截图。

[![itsjzt image](img/391ea7f190675a0e1c097528af77b4e6.png)](/itsjzt) [## 我能看看你的终端吗？

### saura BH Sharma Feb 28 ' 181min read

#discuss #windows #mac](/itsjzt/can-i-see-your-terminal--62e)

你的终端需要更多的瑞恩·高斯林吗？我很确定你知道。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[kori roys](https://github.com/koriroys)/[goslip](https://github.com/koriroys/gosleap)

### 让瑞恩·高斯林跳过你的屏幕

<article class="markdown-body entry-content container-lg" itemprop="text">

# 戈斯利普

仅适用于 OSX

## 使用

[![ryan gosling jumps across the screen](img/2cc0709124def96a114cee75d7fb1150.png)T2】](https://camo.githubusercontent.com/62af169605ecfdf78242ce4fbc490523ea7f3cf4/68747470733a2f2f7261772e6769746875622e636f6d2f6b6f7269726f79732f676f736c6561702f6d61737465722f676f736c6561702e676966)

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'gosleap' 
```

然后执行:

```
$ bundle 
```

或者自己安装:

```
$ gem install gosleap 
```

## 贡献的

1.  叉它([http://github.com/](http://github.com/)/goslip/fork)
2.  创建你的特征分支(`git checkout -b my-new-feature`)
3.  提交您的更改(`git commit -am 'Add some feature'`)
4.  推到树枝上(`git push origin my-new-feature`)
5.  创建新的拉式请求

</article>

[View on GitHub](https://github.com/koriroys/gosleap)

星期五快乐！在下面的评论中分享你最喜欢的终端增强功能吧！