# 我的 Bash 颜色设置如何破坏 edeliver

> 原文：<https://dev.to/ktravers/how-my-bash-color-settings-broke-edeliver-4p13>

是的，你没看错。我的 bash 颜色设置破坏了我的团队用来部署我们的[药剂](https://elixir-lang.org/)应用的工具 [edeliver](https://github.com/edeliver/edeliver) 。

现在，任何修补过他们的`.bash_profile` 的人都知道有无数种方法可以完全破解你的系统。但是这个 bug 被伪装得很好，隐藏在一个常见的、看似良性的 bash 设置中，我已经安装了两年半，没有出现任何问题——一个 bash 设置，你现在可能也已经安装在你的机器上了😱

但是不要担心，我追踪到了这个小家伙，所以继续读下去，省得你和我一样麻烦。对于那些赶时间的人来说，这里是 TL；博士。

*   [问题](#the-problem)
*   [调试步骤](#debugging-steps)
*   [转折点](#the-turning-point)
*   [解释](#the-explanation)
*   [解决方案](#the-solution)
*   [学习](#learnings)

## 问题是

我刚刚开始着手我们的一个 Elixir 项目，尽我最大的努力同时学习一个新的代码库和一门新的语言。在部署变更之前，一切都进行得很顺利。我可以[发布](https://github.com/edeliver/edeliver#build-commands)，没问题，但是我不能[部署](https://github.com/edeliver/edeliver#deploy-commands)；该命令每次都会失败，输出如下:

```
$ mix edeliver deploy release to production --version=X.X.X-XXX-XXXXXXX --verbose

DEPLOYING RELEASE OF MY_APP APP TO PRODUCTION HOSTS

-----> Authorizing hosts
-----> Authorizing release store host
-----> Authorizing deploy hosts on release store
-----> Uploading archive of release X.X.X-XXX-XXXXXXX from remote release store
Uploading release file failed
  source: /home/deployer/my_app/builds/complete/my_app_X.X.X-XXX-XXXXXXX.release.tar.gz on deployer@host
  destination: /home/deployer/my_app/my_app_X.X.X-XXX-XXXXXXX.tar.gz on deploy hosts 
```

Enter fullscreen mode Exit fullscreen mode

我的队友*可以使用相同的命令*进行部署；我是唯一一个得到这个错误的人。我试着从我的工作和个人电脑上运行这个命令，它在两台机器上都以相同的方式出错(我使用共享的[点文件](https://github.com/ktravers/dotfiles)，所以这并不奇怪)。

## 调试步骤

*   [硬复位](#hard-reset)
*   [获取详细信息](#get-verbose)
*   [手动解决方法](#manual-workaround)
*   [SSH 健全性检查](#ssh-sanity-check)

### 1.硬重置

每当我遇到奇怪的错误时，我第一个求助的方法就是:重新开始。我从我的机器上删除了项目目录，重新克隆了它，并尝试部署主分支(它已经在生产中运行了)。没有骰子。

**结果:**

✅确认问题与我在代码库中更改的内容无关

❌没有工作。部署仍然失败。

### 2.变得冗长

我运行带有`--verbose`标志的 deploy 命令，但是 edeliver 错误消息没有告诉我关于*命令失败的原因*(稍后关于[的更多信息](#learnings))。我需要更多信息。

在我的队友[史蒂文](http://hostiledeveloper.com/)的出色建议下，我打开了`my_app/deps/edeliver`，通过搜索错误消息(“上传发布文件失败”)找到了失败的命令，并通过添加`-vvvvv`选项(更`v`= =更详细):
将其切换到详细调试模式

```
# https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L685-L759

# copies the release archive to the production hosts to the given path.
upload_release_archive() {
  # lotsa code, omitted here for brevity
  ssh -A -vvvvv -o ConnectTimeout="$SSH_TIMEOUT" "$_release_store_host" "$_remote_job  $SILENCE" || \
    error "Uploading release file failed\n source: ${_release_store_path%%/}/${_release_file} on $_release_store_host\n destination: $_dest_file_name on deploy hosts"
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我对这个脚本试图(和失败)做什么有了更多的了解。老实说，可能太多的洞察力。在详细调试模式下重新运行 deploy 命令得到了大约 200 行输出:

```
$ mix edeliver deploy release to production --version=X.X.X-XXX-XXXXXXX --verbose

DEPLOYING RELEASE OF MY_APP APP TO PRODUCTION HOSTS

-----> Authorizing hosts
-----> Authorizing release store host
-----> Authorizing deploy hosts on release store
-----> Uploading archive of release X.X.X-XXX-XXXXXXX from remote release store
OpenSSH_7.4p1, LibreSSL 2.5.0
debug1: Reading configuration data /Users/kate/.ssh/config
debug1: /Users/kate/.ssh/config line 1: Applying options for *
debug1: Reading configuration data /etc/ssh/ssh_config
debug2: resolving "xxx.xxx.xx.xxx" port XXX
debug2: ssh_connect_direct: needpriv 0
debug1: Connecting to xxx.xxx.xx.xxx [xxx.xxx.xx.xxx] port XXX.
debug2: fd 3 setting O_NONBLOCK
debug1: fd 3 clearing O_NONBLOCK
debug1: Connection established.
debug3: timeout: 99999969 ms remain after connect
debug1: identity file /Users/kate/.ssh/id_rsa type 1

### approx 180 more lines...

debug3: send packet: type 1
debug1: fd 0 clearing O_NONBLOCK
debug1: fd 1 clearing O_NONBLOCK
debug3: fd 2 is not O_NONBLOCK
Transferred: sent 4624, received 2996 bytes, in 0.2 seconds
Bytes per second: sent 20610.0, received 13353.7
debug1: Exit status 1
Uploading release file failed
  source: /home/deployer/my_app/builds/complete/my_app_X.X.X-XXX-XXXXXXX.release.tar.gz on deployer@host
  destination: /home/deployer/my_app/my_app_X.X.X-XXX-XXXXXXX.tar.gz on deploy hosts 
```

Enter fullscreen mode Exit fullscreen mode

Steven 在他的机器上做了同样的事情，所以我们有他的成功调试日志作为比较。

他的输出也给了我们另一条有价值的信息:由 [`upload_release_archive`函数](https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L689)构建的实际 bash 命令 [`$_remote_job`](https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L754-L755) ，也就是将发布从我们的构建服务器复制到我们的生产服务器的那个，也就是抛出错误的那个。下一步我们会追踪这条线索。

**结果:**

✅以冗长调试模式更好地输出

✅确认了确切的故障点

✅学会了一种新的调试技术(感谢史蒂文！)

❌很难从噪音中分离出信号

### 3.手动解决方法

我们已经查明了在本地失败的命令，那么为什么不尝试直接从构建服务器运行它呢？

我通过 ssh 进入我们的构建服务器，切换到我们的部署者用户，运行上面的 [`$_remote_job`命令](https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L754-L755)。你知道吗，成功了！正如预期的那样，发布文档从构建服务器复制到了我们的生产服务器。

我回到我的本地机器，注释掉 edeliver 脚本中的 [`upload_release_archive`函数](https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L689)(因为归档文件已经被复制了)，然后运行 deploy 命令。成功！

所以现在事情变得有趣了。当我作为我的`kate`用户从本地运行这个命令时，它失败了。当我作为`deployer`从我们的构建服务器运行它时，它成功了。结论:我的用户有问题。

**结果:**

✅我们找到了一个可用的(尽管是超手动的)解决方法

✅确认问题不在我们项目的 edeliver 配置中

❌仍然不能从我的本地部署

### 4.健全性检查

我们已经将问题缩小到我的用户，所以下一步是检查我的 ssh 配置。我以我的用户身份登录，确认我的密钥在所需的服务器上...那里没问题。然后我将我的`.ssh/config`文件与我队友的配置进行了比较...也没什么奇怪的。

我还尝试了在删除以下配置文件中的所有内容后进行部署(逐个隔离测试)，但仍然没有成功:

*   `~/.ssh/known_hosts`
*   `~/.bashrc`
*   `~/.profile`

**结局:**

✅确认问题不是出在我的 ssh config 上

❌出主意了

## 转折点

此时，绝望开始袭来。我和我的队友都被难住了，我们已经没有东西可以尝试了。

进入安德烈斯从 [OzoneOps](https://ozoneops.com/) ，我们的 devops 承包商。我把 Steven 和我的输出日志发给了他，他第一个注意到了一个非常重要的区别:在我的输出中，存档文件名中有一堆奇怪的字符。

```
# Filename from Steven's output
my_app_X.X.X-XXX-XXXXXXX.release.tar.gz

# Filename from Kate's output
my_app_\033[4;38m\033[KX.X.X-XXX-XXXXXXX\033[m\033[K.release.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么事？这些额外的字符来自哪里？

谷歌快速搜索发现，这些是终端转义序列，或 [ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code)。终端通常将这些序列解释为函数，而不是字符，所以您可以使用它们来格式化输出，比如给`grep`或`ls`命令的输出添加颜色。

我喜欢在我的终端输出中使用一些颜色，所以早在一天前(比如，两年半前)，我就在我的`.bash_profile`中添加了设置来给我的`ls`和`grep`输出着色。

```
export LSCOLORS=gxxxxxxxcxxxxxcxcxgxgx
export GREP_OPTIONS='--color=always'
export GREP_COLOR='4;38' 
```

Enter fullscreen mode Exit fullscreen mode

我从来没有想过这些场景...直到现在。原来他们一直是一个定时炸弹，只是在等待这个特定的场景。

## 解释

上面的设置通过在`ls`和`grep`命令的输出中添加 ANSI 转义码来发挥其着色的魔力。例如，让我们在下面的`example.txt`上运行`grep`。

```
$ cat example.txt
abcdef 
```

Enter fullscreen mode Exit fullscreen mode

```
$ grep abc example.txt
abcdef

$ grep --color=always abc example.txt
abcdef 
```

Enter fullscreen mode Exit fullscreen mode

似乎无害，对吗？转义码被正确地“转义”，被解释为函数而不是字符...除非你的终端不知道如何解释这个序列。例如，看看当您将彩色的`grep`输出通过管道传输到`less` :
时会发生什么

```
$ grep --color=always abc example.txt | less
ESC[01;31mabcESC[00mdef
(END) 
```

Enter fullscreen mode Exit fullscreen mode

问题来了。如果您将彩色输出通过管道传输到像`less`这样不知道如何解释它的函数中，转义码将被视为普通的 ol 字符。如果你在一个不支持你通过`LSCOLORS`或`GREP_COLOR`变量设置的任何转义码的终端中给`ls`或`grep`着色，也会发生同样的事情。

这就是我在 [edeliver](https://github.com/edeliver/edeliver) deploy 脚本中遇到的情况。

回头看看 [edeliver 源代码](https://github.com/edeliver/edeliver/blob/master/libexec/erlang#L662)，归档的发布文件名是从`ls`和`grep`命令的输出构建的。因为我在我的`.bash_profile`中设置了这些着色变量，所以我的机器在输出中添加了颜色转义码，这些代码被“误解”为发布归档文件名中的额外字符，破坏了部署。

✋

。

。

。

🎤*麦克下降:

## 解决方案

从您的`.bash_profile` :
中删除任何`ls`或`grep`颜色设置

```
# ☠️☠️☠️ incompatible with edeliver deploy script ☠️☠️☠️
export LSCOLORS=gxxxxxxxcxxxxxcxcxgxgx
export GREP_OPTIONS='--color=always'
export GREP_COLOR='4;38' 
```

Enter fullscreen mode Exit fullscreen mode

【更新】基于[的这个](https://dev.to/carlfish/comment/35l8)来自[查尔斯·米勒](https://dev.to/carlfish)的有用评论，更简单的解决方法就是把`export GREP_OPTIONS='--color=always'`改成`export GREP_OPTIONS='--color=auto'`。

## 学习

调试是困难的，尤其是当你在一个新的代码库和/或新的语言中工作的时候。有一件事真的可以拯救你，那就是好的错误消息，这是 Elixir 擅长做的，edeliver 可以做得更好。

接下来的步骤将是为 [edeliver](https://github.com/edeliver/edeliver) 提出这个问题，并尝试改进一些错误消息(也许是修复这个无意义的转义序列...我认为我们只需要在运行 deploy 命令之前禁用颜色设置)。

## 资源

*   [官方 edeliver 调试提示](https://github.com/edeliver/edeliver#help)
*   [关于 ANSI 转义码的更多信息](https://en.wikipedia.org/wiki/ANSI_escape_code)
*   [更多关于 GNU `grep`环境变量](https://www.gnu.org/software/grep/manual/html_node/Environment-Variables.html)
*   [edeliver 上报告了类似问题](https://github.com/edeliver/edeliver/issues/14)
*   [关于 edeliver 错误消息的更多详细信息](https://github.com/edeliver/edeliver/issues/80)