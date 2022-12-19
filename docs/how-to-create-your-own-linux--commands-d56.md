# 如何创建自己的 Linux 命令

> 原文：<https://dev.to/xeroxism/how-to-create-your-own-linux--commands-d56>

[![linux_alias_command_banner](img/cde95c459c12fd4cc859f82e916cce24.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/10/linux_alias_command_banner.png?ssl=1)

谁不喜欢拥有创造事物的力量呢？在众多能力中，Linux 给了你创建命令的能力；或者在您选择的任何新命令中包含默认的 Linux 命令。

实际上，你不需要创建新的命令(如**“cat”、“ls”或“touch”**)，这些命令可以在任何 Linux 发行版上使用。远非如此。\** *使眼色** *。相反，您可以在本地系统上创建一些命令别名(或者您可以说备用命令)供个人使用——使用 Linux **alias* *命令。

当您必须键入长命令时，这尤其有用；当你不得不经常这样做时，这会变得很累。它还可以帮助你用容易记忆的语言来表达命令。

没有太多的麻烦，让我们开始吧。

### 第一步:

添加一个名为"的文件。bash_aliases”(如果不存在就创建)，放在您的主目录(/user/home)中。

### 第二步:

然后以常规形式添加任何命令别名(替代命令):

```
alias command_alias = “actual command” 
```

然后保存文件。

其中“actual_command”是在命令行中键入“command_alias”(您赋予它的新名称)时要执行的命令。

## 举例:

**> >** 若此”。bash_aliases "文件不存在创建它:

```
touch .bash_aliases 
```

**> >** 在文件中添加“**sudo/etc/init . d/Apache 2 start”**命令作为别名:

```
alias apacheon = sudo /etc/init.d/apache2 start” 
```

**> >** 保存文件。

新命令别名“ **apacheon** ”映射到实际命令(**sudo/etc/init . d/Apache 2 start**)。这意味着，每当您在终端中输入“ **apacheon** ”时，bash 都会将其解释为“**sudo/etc/init . d/Apache 2 start**”，然后执行该命令。

您可以在文件中添加任意数量的别名。

## 例子:

```
alias apachestop = “/etc/init.d/apache2 stop”

alias systemupdate = “sudo apt-get -y update”

alias wscan = “sudo iwlist wlan0 scan | more” 
```

试着用几个化名自己看看吧！！！

快乐的 Linux！

帖子[如何创建自己的 Linux 命令](https://fossnaija.com/how-to-create-your-own-linux-commands/)首先出现在 [Foss Naija](https://fossnaija.com) 上。