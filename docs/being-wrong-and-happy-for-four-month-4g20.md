# 错误而快乐了四个月

> 原文：<https://dev.to/jrwren/being-wrong-and-happy-for-four-month-4g20>

呃。

昨天想用 node 和 npm 写点东西。这是我很少做的事情。我想在运行 ubuntu 的旧服务器上使用它。我查了 node。它就在那里。我经营 npm。它不在那里。我检查了节点二进制文件和它来自的 deb 包。是从 ubuntu 18.04 回购来的。我可以安装 npm 来获得旧版本的 npm，或者我可以将节点从 ubuntu repo 升级到包含新版本 npm 的 nodesource repo。我选择了后者。

我的启蒙就是从这里开始的。

四个月前，我很高兴地看到在运行 add-apt-repository 之后不再需要`apt update`命令。当我在[https://itsfoss.com/ubuntu-18-04-release-features/](https://itsfoss.com/ubuntu-18-04-release-features/)上读到它时，我认为“-u”现在是默认行为。这将是实现这一特性的最有效的方式。我草率地下了结论。

让我回到 3 年前。

我正在开发一个云部署工具，它部署了许多新的 ubuntu 映像，并在上面安装了一些东西。这意味着大量的 apt-get 更新和 add-apt-repository 调用。它很慢。当运行在一个有慢速互联网和非本地存储库的云上时，速度甚至更慢。为了从 apt-get 更新中获取相同的结果，所有这些 http 往返都快把我逼疯了。我不喜欢慢的东西。一定有办法加快速度。

我为 add-apt-repository 编写了'-u '特性，我提交了它，它被接受了，并表示感谢。我非常高兴。我觉得我让这个世界变得更好了，即使对我们这些技术人员来说只是一丁点儿。“-u”特性的工作方式是只为新添加的存储库而不是像 apt-get update 那样为所有存储库获取和合并新的包信息。它可以从添加存储库的部署中节省一分钟或更多的时间，再加上使用多个机器或系统容器的部署，它会成倍增加。

回到昨天的 npm。

我运行 add-apt-repository 命令时没有使用-u，因为我认为不应该需要它...

```
sudo add-apt-repository 'deb https://deb.nodesource.com/node_8.x bionic main' 
```

Enter fullscreen mode Exit fullscreen mode

慢点。

它非常慢。

事实证明我关于-u 是违约的结论是错误的。现在缺省的是一个完整的(通常是不必要的)apt-get 更新。呃。失望。

结论:继续将'-u '与 add-apt-repository 一起使用。

脚注:

我使用这些命令在 ubuntu 仿生系统上获取 nodejs。对于 10.x 存储库，node_8.x 可以更改为 node_10.x。仿生可以是任何拉伸，杰西，巴斯特，锡德，可靠，xenial，精确。*我认为有些组合可能不起作用，但只要你在足够新的发行版上，来自 nodesource 的较新的节点 repos 将会起作用。

```
curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
sudo add-apt-repository -u 'deb https://deb.nodesource.com/node_8.x bionic main'
sudo apt purge npm # may not be required if it is not already installed.
sudo apt install nodejs 
```

Enter fullscreen mode Exit fullscreen mode