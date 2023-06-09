# 远程 SSH 到 Windows 上的 Ubuntu 虚拟机

> 原文：<https://dev.to/awwsmm/setting-up-an-ubuntu-vm-on-windows-server-2g23>

我们最近有了一台新的 Windows 服务器，我想在里面安装一个 Ubuntu VM。试了几次之后，我成功地按照我想要的方式安装了所有的东西。特别难以设置的一件事是 MobaXTerm(运行在我们公司子网的 Windows 7 机器上，是附近一所大学网络的一部分)和运行在 Windows 服务器机器上的 Ubuntu VM(实际上在大学网络的防火墙后面，它向 Windows 服务器机器转发一个端口以允许 SSH 连接通过)之间的 SSH 连接。真是一团糟。但是现在能用了！

如果你想知道我是如何管理各种 JVM 语言，处理多个 Python 版本，轻松安装 Hadoop 和 Spark，并保持我的`$PATH`明智的，请继续阅读！

# 在 Windows 上设置 Linux 虚拟机

我在 [VirtualBox](https://www.virtualbox.org) 上安装了 [Ubuntu 18.04.1 LTS](https://www.ubuntu.com/download/desktop) ，选择了“最小安装”，允许安装程序“在安装 Ubuntu 的同时下载更新”。尽管如此...

[![](img/4cea5d9ecc896f703b2a1fc68857bee7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LJxIx7G4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r29pbe6gyg5z95eyqkze.PNG)

当然可以。

[![](img/a754bef8358998078eb72f93eb877985.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NgFEbJiP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50nff7wjakbi2o7jfjkx.PNG)

又来了？好吧。

我知道我以后需要在 SSH 中这样做，所以当它重新启动时，我在 VirtualBox 中设置了一个网络适配器。我点击了 VirtualBox 屏幕右上角的“全球工具”:

[![](img/46861e2f733909db936ff0e43c829ab1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qUInJUQw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xaqeapxjs803s52imtw.PNG)

点击左上角的“创建”:

[![](img/2dddbd21420a2e5a51b787dc421553c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AlZpFeN6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nczvow5o5z43dv5fcvix.PNG)

点击“是”:

[![](img/d2eeebeb5599b21b4206dff9f9272f56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ycF_zevw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4dv9dbptej3xxbnxksl1.PNG)

并单击菜单栏中的“属性”以打开屏幕底部的属性选项卡:

[![](img/8a23a0b06be74ba24a324afdf9deb0c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ZVUtD_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gdogcxjagkktevlxzl2m.PNG)

[![](img/ed69b6ac5ba636abfac0cb1e2c0a70f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JE220qEx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7dbxwqvnocvte006l6sf.PNG)

我记下了 IPv4 地址(它应该类似于`192.168.___.1`)，并在最右栏的“DHCP 服务器”下选中“启用”。现在一切都设置好了，我回到 VM 内部。(注意，有时候，在这些教程中，VM——在我的例子中是 Ubuntu——被称为“来宾”操作系统，而运行它的操作系统——在我的例子中是 Windows Server——被称为“主机”操作系统。)

我在 Ubuntu 中打开终端，安装一些基础软件:

```
$ sudo apt-get update && sudo apt-get upgrade -y
$ sudo apt-get install net-tools tree git openssh-server ifupdown ssh curl yum -y 
```

Enter fullscreen mode Exit fullscreen mode

注意，`-y`标志意味着当 Ubuntu 询问时，你不必明确地输入“Y”...

> `After this operation, ___ of additional disk space will be used.`
> T1】

最后，我更改了`root`账户的密码，这样我就可以在`su`之后`root`了:

```
$ sudo passwd root
Enter new UNIX password: <type>
Retype new UNIX Password: <type>
passwd: password updated successfully
$ su -
Password: <type>
root$ exit
$ # back to normal command prompt 
```

Enter fullscreen mode Exit fullscreen mode

# 安装 JVM 东西和 Haskell

我用 [SDKMAN！](https://sdkman.io/)安装我大部分基于 JVM 的东西(比如 Java，Scala 等。).真的很简单:

```
$ curl -s "https://get.sdkman.io" | bash
$ source $HOME/.sdkman/bin/sdkman-init.sh
$ sdk version # to check that it was installed 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到所有可以通过 SDKMAN 安装的软件！同:

```
$ sdk list 
```

Enter fullscreen mode Exit fullscreen mode

如果可以安装多个版本，可以用
列出

```
$ sdk list <software> 
```

Enter fullscreen mode Exit fullscreen mode

例如，`sdk list java`返回

```
12.ea.15-open
11.0.1.-zulu
11.0.1-open
10.0.2-zulu
10.0.2-open
... 
```

Enter fullscreen mode Exit fullscreen mode

...诸如此类。我将安装一个稳定的遗留 Java 版本(Java 8)和最新的 LTS 版本(Java 11):

```
$ sdk install java 11.0.1-open
$ sdk install java 8.0.191-oracle 
```

Enter fullscreen mode Exit fullscreen mode

使用
查看特定软件的当前版本

```
$ sdk list <software> # OR
$ sdk current <software> 
```

Enter fullscreen mode Exit fullscreen mode

用
设置默认版本

```
$ sdk default <software> <version> 
```

Enter fullscreen mode Exit fullscreen mode

或者用
更改当前版本(仅对当前 shell 有效)

```
$ sdk use <software> <version> 
```

Enter fullscreen mode Exit fullscreen mode

尝试在 Java 版本之间来回切换，并通过运行`java -version`来验证版本是否已经更改。此外，Java shell`jshell`在 Java 9 之前并不存在，所以如果您切换到 Java 8 并尝试命令`jshell`，您会得到一个错误(但在 Java 11 中不会得到这个错误)。

接下来，我安装了一堆其他 JVM/Java 相关的东西:

```
$ sdk install groovy  # JVM language
$ sdk install kotlin  # JVM language
$ sdk install maven   # Java build tool
$ sdk install sbt     # Scala build tool
$ sdk install scala   # JVM language
$ sdk install spark   # Scala shell 
```

Enter fullscreen mode Exit fullscreen mode

所有这些软件都安装在`$SDKMAN_DIR/candidates/`中，默认情况下是`$HOME/.sdkman/candidates/`。你需要为每个软件选择默认版本。使用
查看您当前使用的*所有*版本

```
$ sdk current

Using:

java: 11.0.1-open 
```

Enter fullscreen mode Exit fullscreen mode

我目前只设置了 Java。让我为所有这些东西选择默认版本:

```
$ sdk default  groovy  2.5.3
$ sdk default  kotlin  1.3.0
$ sdk default  maven   3.5.4
$ sdk default  sbt     1.2.6
$ sdk default  scala   2.12.7
$ sdk default  spark   2.3.1 
```

Enter fullscreen mode Exit fullscreen mode

最后——SDK man 上的说明遗漏了一些东西！网站——你需要再次“获取”脚本。之后，你应该会在`sdk current` :
中看到你所有的新软件

```
$ source $HOME/.sdkman/bin/sdkman-init.sh
$ sdk current

Using:

groovy: 2.5.3
java: 11.0.1-open
kotlin: 1.3.0
maven: 3.5.4
sbt: 1.2.6
scala: 2.12.7
spark: 2.3.1 
```

Enter fullscreen mode Exit fullscreen mode

通过使用适当的`version`标志或命令行参数:
调用它们，验证它们已经正确安装

```
$ groovy --version
...
Groovy Version: 2.5.3 ...

$ java -version
openjdk version "11.0.1" 2018-10-16
...

$ kotlin -version
Kotlin version 1.3.0-release-212 ...

$ mvn --version
Apache Maven 3.5.4 ...
...

$ sbt sbtVersion
...
[info] 1.2.6

$ scala -version
Scala code runner version 2.12.7 ...

$ spark-submit --version
Welcome to ... version 2.3.0 ... 
```

Enter fullscreen mode Exit fullscreen mode

...就是这样！还可以查看`spark-shell`、`groovysh`等。工作。(注意`spark-shell`可能会崩溃，除非你使用的是 Java 8)。

# 安装哈斯克尔和阴谋集团

Haskell 真的很好装:

```
$ sudo apt-get install haskell-platform -y
$ ghci 
```

Enter fullscreen mode Exit fullscreen mode

这也安装了 Haskell 包管理器，`cabal` :

```
$ cabal --version
cabal-install version 1.24.0.2
... 
```

Enter fullscreen mode Exit fullscreen mode

# 安装和配置 Hadoop

接下来，我找到了 Hadoop 的最新稳定版本[，记下它的 URL，并使用](http://hadoop.apache.org/releases.html)[指南](https://www.digitalocean.com/community/tutorials/how-to-install-hadoop-in-stand-alone-mode-on-ubuntu-16-04):
下载它

```
$ wget http://ftp.heanet.ie/mirrors/www.apache.org/dist/hadoop/common/hadoop-2.8.5/hadoop-2.8.5.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

...和 untar 到`/usr/local/hadoop`，将输出重定向到`/dev/null` :

```
$ sudo mkdir /usr/local/hadoop
$ sudo tar -xzvf hadoop-2.8.5.tar.gz -C /usr/local/hadoop >/dev/null 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们用 SDKMAN 改 Java 版本的时候！，它改变`$JAVA_HOME`系统变量:

```
$ echo $JAVA_HOME
/home/andrew/.sdkman/candidates/java/11.0.1-open
$ sdk use java 8.0.191-oracle
$ echo $JAVA_HOME
/home/andrew/.sdkman/candidates/java/8.0.191-oracle 
```

Enter fullscreen mode Exit fullscreen mode

Hadoop 需要访问 Java 库。如果我们希望 Hadoop 使用 Java 的默认版本，我们可以在`/usr/local/hadoop/hadoop-2.8.5/etc/hadoop/hadoop-env.sh`中使用`$JAVA_HOME`。如果我们想让它坚持特定的 Java 版本(比如 Java 8)，我们可以使用一个静态值，比如`$SDKMAN_DIR/candidates/java/8.0.191-oracle`。我现在不去管这件事了。(有关更多信息，请参见前面的链接。)

为了方便使用，我设置了一个`HADOOP_HOME`变量:

```
$ export HADOOP_HOME=/usr/local/hadoop/hadoop-2.8.5 
```

Enter fullscreen mode Exit fullscreen mode

...并运行 Hadoop 的 MapReduce 示例之一以确保它正常工作:

```
$ mkdir ~/mrtest
$ cp $HADOOP_HOME/etc/hadoop/*.xml ~/mrtest
$ $HADOOP_HOME/bin/hadoop jar \
$   $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.5.jar \
$   grep ~/mrtest ~/grep_example 'principal[.]*' 
```

Enter fullscreen mode Exit fullscreen mode

如果运行成功，输出将以类似于
的内容结束

```
 File Input Format Counters
                Bytes Read=151
        File Output Format Counters
                Bytes Written=37 
```

Enter fullscreen mode Exit fullscreen mode

键入:
可以看到结果

```
$ cat ~/grep_example/*
6       principal
1       principal. 
```

Enter fullscreen mode Exit fullscreen mode

Hadoop 管用！

# 用`pyenv`管理多个 Python 版本

接下来，我安装`pyenv`来管理多个 python 版本。[用
安装必备软件](https://github.com/pyenv/pyenv/wiki/Common-build-problems)

```
$ sudo apt-get install make build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev wget libncurses5-dev libncursesw5-dev \
    llvm xz-utils tk-dev libffi-dev liblzma-dev -y 
```

Enter fullscreen mode Exit fullscreen mode

然后用
安装`pyenv`

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
$ export PYENV_HOME=$HOME/.pyenv
$ pyenv init - 
```

Enter fullscreen mode Exit fullscreen mode

验证它与
一起工作

```
$ $PYENV_HOME/bin/pyenv versions
* system (set by /home/andrew/.pyenv/version) 
```

Enter fullscreen mode Exit fullscreen mode

使用
安装其他版本

```
$ $PYENV_HOME/bin/pyenv install 2.7.15
$ $PYENV_HOME/bin/pyenv install 3.7.1 
```

Enter fullscreen mode Exit fullscreen mode

再次查看可用版本:

```
$ $PYENV_HOME/bin/pyenv versions
* system (set by /home/andrew/.pyenv/version)
  2.7.15
  3.7.1 
```

Enter fullscreen mode Exit fullscreen mode

切换默认版本，并验证您已经切换:

```
$ $PYENV_HOME/bin/pyenv global 2.7.15
$ $PYENV_HOME/shims/python --version
Python 2.7.15

$ $PYENV_HOME/bin/pyenv global 3.7.1
$ $PYENV_HOME/shims/python --version
Python 3.7.1 
```

Enter fullscreen mode Exit fullscreen mode

# 定制`~/.bashrc`

我不会向我的`$PATH`添加任何东西，直到我确信我明白它在做什么。很容易有一个巨大的`$PATH`,它只包含每个目录，却不知道实际的可执行文件来自哪里。我还试图为我安装的每一个软件都准备好`_HOME`系统变量(这通常是*包含*和`bin/`的目录)，这样我以后可以更容易地找到它们。所以在我的`.bashrc`中，我现在将添加以下内容:

```
 ##----------------------------------------------------------------------------
  ##  handled by SDKMAN:
  ##----------------------------------------------------------------------------

  export SDKMAN_HOME=$SDKMAN_DIR
  #      GROOVY_HOME
  #        JAVA_HOME
  #      KOTLIN_HOME
  #       MAVEN_HOME
  #         SBT_HOME
  #       SCALA_HOME
  #       SPARK_HOME

  ##----------------------------------------------------------------------------
  ##  other important directories:
  ##----------------------------------------------------------------------------

  export PYENV_HOME=$HOME/.pyenv
  export HADOOP_HOME=/usr/local/hadoop/hadoop-2.8.5
  export HADOOP_CLASSPATH=$HADOOP_HOME/share/hadoop/common

  ##----------------------------------------------------------------------------
  ##  JAR files
  ##----------------------------------------------------------------------------

  export JAVA_JARS=$SDKMAN_HOME/candidates/scala/jars
  export SCALA_JARS=$SDKMAN_HOME/candidates/java/jars

  ##----------------------------------------------------------------------------
  ##  JAR lists
  ##----------------------------------------------------------------------------

  export JAVA_JARS_LIST=".:$JAVA_JARS/\*"
  export SCALA_JARS_LIST=".:$JAVA_JARS/\*:$HADOOP_CLASSPATH/\*:$SCALA_JARS/\*"

  ##----------------------------------------------------------------------------
  ##  update PATH
  ##----------------------------------------------------------------------------

  export PATH=$PATH:/bin:/sbin
  export PATH=$PATH:/usr/bin:/usr/sbin
  export PATH=$PATH:/usr/local/bin:/usr/local/sbin

  export PATH=$PATH:$HADOOP_HOME/bin
  export PATH=$PATH:$HADOOP_HOME/sbin
  export PATH=$PATH:$PYENV_HOME/bin 
```

Enter fullscreen mode Exit fullscreen mode

将这些快捷方式加载到 shell 中，然后为`JAVA_JARS`和`SCALA_JARS`创建目录:

```
$ source ~/.bashrc
$ mkdir $JAVA_JARS
$ mkdir $SCALA_JARS 
```

Enter fullscreen mode Exit fullscreen mode

我试着把我所有的文件放在一个地方。

现在我们在`PATH`上有了所有这些`bin/`目录，我们可以只写:

```
$ pyenv versions
$ hadoop version 
```

Enter fullscreen mode Exit fullscreen mode

...而不是更长的:

```
$ $PYENV_HOME/bin/pyenv versions
$ $HADOOP_HOME/bin/hadoop version 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，**当你在命令行上运行一些东西时，你应该总是知道你调用的是哪个二进制文件。这样做(在我们编辑路径之前验证软件是否工作)有助于您理解您正在运行的软件实际上“生活”在您系统的什么地方。**

正如 Jess 在这篇文章中提到的:

[![thejessleigh](img/f469a39e68e830438247062fe0064d78.png)](/thejessleigh) [## 基本生活质量终端改善

### Jess un rein 10 月 26 日 188 分钟阅读

#bash #terminal #explainlikeimfive #tutorial](/thejessleigh/essential-quality-of-life-terminal-improvements-4pa4)

...`~/.bash_profile`仅用于*登录*shell，而`~/.bashrc`用于交互式非登录 shell。这意味着`~/.bashrc_profile`只有当你通过 ssh 或其他方式登录到机器时才会被激活。大多数时候这可能不是你想要的。我把我所有的自定义 shell 脚本、别名等等。在`~/.bashrc`里。

我也有很多在启动时获得的定制 shell 脚本，但我想那是另一篇文章了！至此，你应该能够运行所有主要的 JVM 语言并管理它们的版本，用 Maven 和 sbt 管理 Java 和 Scala 项目，用 Apache Hadoop 和 Spark 分析和存储“大数据”，用 Haskell 和`ghci` shell 编写代码，用`pyenv`轻松运行多个版本的 Python。

在关于我的 shell 脚本的文章中，我还会谈到我如何定制颜色、字体等。在我的壳里为了最大的美丽。

# 从 Windows 登录远程 Linux 虚拟机

现在要设置的最后一件事是我的本地 Windows PC 和由 Ubuntu VM 托管的远程 Windows 服务器之间的 SSH 连接。请注意，这些说明是专门针对我的设置的，可能不适合您，但是如果这与您想要完成的类似，那么值得一试。我做的第一件事是在虚拟机中运行`ifconfig`:

```
$ ifconfig
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
...
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
... 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到两个类似于上面的连接- `enp0s3`和`lo`。我们需要添加第三个，所以让我们关闭虚拟机并返回到 VirtualBox。返回“机床”，从左侧列表中选择您的虚拟机，然后单击“设置”按钮:

[![](img/cb7b019ed7e4e9c63ff36532c6918c11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MJUfqibM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7od33whvn0hnwrc4lpdg.PNG)

从左侧菜单中单击“网络”，然后单击“适配器 2”。选中“启用网络适配器”，然后在“连接到:”旁边，选择“仅主机适配器”:

[![](img/fe292457080d29218cad647b19d3e7b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6yqbeiQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/40hkk6j9t381x5h16wgy.PNG)

无论 VirtualBox 在这里填写什么名称都可以。点击“确定”并重启你的虚拟机。现在，当您在 Ubuntu VM 上运行`ifconfig`时，您应该会看到三个连接:

```
$ ifconfig
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
...
enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
...
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
... 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要编辑`/etc/network/interfaces`文件。将其打开为`sudo`，并添加以下行:

```
auto enp0s8
iface enp0s8 inet static
address 192.168.___.10
netmask 255.255.255.0 
```

Enter fullscreen mode Exit fullscreen mode

...其中`___`应该与本演练开始时的网络适配器设置步骤相同。注意，这个 IP 地址的最后一部分是`.10`，上面是`.1`。**最重要的步骤**是在 VirtualBox 中设置一个端口转发。这允许您 ssh 到主机(在我的例子中是 Windows 服务器)，并让它将连接转发到 VM(在我的例子中是 Ubuntu)。

要在 VirtualBox 中设置端口转发，请转到“Machine Tools”页面，确保选择了您的虚拟机，然后单击“Settings”，就像我们之前所做的那样。单击左侧菜单上的“网络”，停留在“适配器 1”上，然后打开“高级”部分。点击“端口转发”按钮:

[![](img/aa2ba74613e5e3f446b2a44bb9c3c2cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s51VNlKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s62nize2fg7ub47j1ctr.PNG)

通过单击右上角的绿色“+”号添加新的端口转发规则:

[![](img/0a6567eb182569420db2a0763ec3d245.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E27uNYb3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b239ytnsi7dops6w3vxc.PNG)

这部分在我的设置中有点复杂。我们有一个位于防火墙后面的服务器，所以服务器本身的 IP 地址与我实际进入的 IP 地址不同。这里，我使用服务器的 IP 地址，您可以通过在远程主机(Windows 服务器)上的 Windows `cmd`提示符下运行`ipconfig`找到它:

[![](img/7504c1abaf3ffbd92b5e1688347bc1b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ahuzXz5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vnq5cbmk0ifidous8vgl.PNG)

在我的例子中，服务器在防火墙后面的一个私有局域网上，这个局域网上的服务器 IP 是`192.168.100.100`。我们还在防火墙上设置了端口转发，这样我进入的端口就不同于服务器看到我们试图访问的端口。所以服务器看到我们访问的端口是`22`。一般来说，“主机 IP”和“主机端口”是您试图在远程 Windows 机器(对于我的设置，是 Windows 服务器)上访问的 IP 地址和端口。

“来宾 IP”和“来宾端口”是服务器看到的虚拟机的 IP 地址和端口。来宾 IP 就是我们上面设置的`192.168.___.10`那个，端口可以是任意数字。我尝试使用高价值的质数，但是任何东西都可以。既然这样，我就用`33331`，因为何乐而不为。这只是远程主机用来与虚拟机通信的端口，因此您在这里使用什么并不重要:

[![](img/d1ac68d621aa2fb5bc0a366e3c5c6301.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EZRep2c7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yaju7ads8g83wtzye6wv.PNG)

端口转发规则可以有任何名称。再次点击“确定”和“确定”,回到你的虚拟机。我们需要做的最后一件事是编辑`/etc/ssh/sshd_config`。以 sudo 身份打开，并更改行:

```
#Port 22 
```

Enter fullscreen mode Exit fullscreen mode

至

```
Port 33331 
```

Enter fullscreen mode Exit fullscreen mode

或者你选择的任何端口号。这确保了虚拟机上的 ssh 服务器正在监听该端口上的 ssh 连接。现在，再次运行`ifconfig`，看看与`enp0s8`关联的`inet`地址。我的情况是`192.168.100.111`。但是我们声明`enp0s8`是静态的，并通过编辑`/etc/network/interfaces`获得 IP 地址`192.168.___.10`。让我们使用命令:
关闭并再次打开这个网络适配器

```
$ sudo ifdown enp0s8
$ sudo ifup enp0s8 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您在 Ubuntu VM 上运行`ifconfig`时，您应该会看到您定义为`inet`地址的 IP 地址。你现在应该可以在远程主机(对我来说是 Windows 服务器)上从 Windows `cmd`提示符中`ping`出`enp0s8`的 IP 地址了:

[![](img/eae7914ccad2756af5e4dd3c726658e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3l9PZLH9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aloeo8hro2jv5uzt5qsy.PNG)

我们现在可以从本地 Windows 机器上的 MobaXTerm 远程 SSH 到虚拟机:

```
ssh -p <port> <username>@<IP> 
```

Enter fullscreen mode Exit fullscreen mode

上面的`<port>`是您用来访问远程主机的端口地址。在我的例子中，这是防火墙上打开的端口，它转发到服务器上的端口`22`。`<username>`是您在虚拟机中的用户名(尽管在我的例子中，我在远程主机和虚拟机上使用了相同的名称，`andrew`)。最后，`<IP>`是远程主机的 IP 地址(或者，在我的例子中，是远程主机所在的防火墙的 IP 地址)。

即使你做了所有这些，你可能会得到一个类似这样的错误:

```
WARNING: REMOTE HOST SPECIFICATION HAS CHANGED! 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况是因为我们将远程主机上的端口转发到虚拟机的端口，但是我们在`%MOBAXTERM_HOME%\home\.ssh\known_hosts`中已经有了一个与该 IP 地址相关的键。只需打开该文件并删除任何以该 IP 开头的行。我这样做了，然后(下面的 IP 地址和端口号仅用于说明目的):

```
$ ssh -p 11111 andrew@156.77.221.23
Permanently added `[156.77.221.23]:11111' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.1\. LTS (GNU/Linux 4.15.0-38-generic x86_64)
...
andrew@ubuntuvm:~$ 
```

Enter fullscreen mode Exit fullscreen mode

...有用！我知道这种设置非常特殊，可能无法帮助大多数人，但我花了一些时间来弄清楚如何让一切正常运行，如果我能帮助一个人，那就值得了。(此外，我必须将此写下来，以便我的同事将来知道如何做！)让我知道你在下面的想法，感谢你的阅读(如果你已经做到了这一步)！

在以后的文章中，我将讨论如何用方便的函数、别名、字体和颜色来定制我的 shell！敬请期待！