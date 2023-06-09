# 域名系统和一场演变中的政治危机。猫域

> 原文：<https://dev.to/flaque/dns-and-an-evolving-political-crisis-a-saga-of-the-cat-domain-4jh1>

2017 年，西班牙政府关闭了几个顶级域名`.cat`的网站。🔥🐱🔥

要了解原因，让我们了解一下 DNS，互联网是如何工作的，以及西班牙的政治危机。

# 互联网的诞生是为了让加州大学洛杉矶分校的毕业生们能够找到他们在斯坦福的朋友

每一次关于 DNS 的讨论都是从头开始的，因为你对 DNS 了解得越多，就越觉得它从来就没打算变得这么大。

在 20 世纪 60 年代末，ARPA，美国政府的一个分支，成立的目的是为了阻止苏联获得文明 5 的科学胜利，解决了一个问题。

ARPA 把他们早期的一些又大又贵的计算机放在了远离需要它们的地方。

加利福尼亚是一个大地方。

第一个通过网络发送的数据包是从加州大学洛杉矶分校发送到斯坦福研究所的。几年之内，✨的阿帕网✨将不仅连接世界各地，还能与其他几个独立创建的互联网互通。

# 他们只是把所有东西都放在一个文件里

当 ARPAnet 只有几百台计算机连接在一起时，通过一个`HOSTS.TXT`将名字映射到一台计算机上。

如果你在 Mac 或 Linux 电脑上，你在`/etc/hosts`会有一个剩余的`HOSTS.TXT`，这就是让你在浏览器中输入`localhost`而不是`127.0.0.1`的原因。

如果你想给 ARPAnet 添加一个新名字，你可以发邮件给斯坦福研究院(SRI ),它会被编译进`HOSTS.TXT`。

网络上的任何人都可以检索该文件，每个主机都有自己的副本。

当我们开始超越几百台主机时，事情变得困难了。有人可以轻易地覆盖现有的主机；没有保证唯一的名称。

因此，如果我们的主机文件看起来像这样:

```
3.0.0.1  nuclearpoweranddishwashers 
12.0.0.1 ringadingding
... some hundreds of sites 
```

Enter fullscreen mode Exit fullscreen mode

有人可能会过来用他们自己的地址
覆盖`ringadingding`

```
25.0.0.1 ringadingding 
```

Enter fullscreen mode Exit fullscreen mode

另外，每个`HOSTS.TXT`看起来都不一样，因为新网站的添加速度比人们刷新文件的速度要快。

# 电脑人用树解决问题🌲

这些问题产生了域名系统。从根本上说，DNS 是一个检索名称的分层树。

就像文件系统将东西分成文件夹一样，DNS 将东西分成*域*。在每棵树的顶端是一个**顶级域名(TLD)** ，类似于`.edu`或`.com`。

DNS 将域的存储*和管理*委托给它下面的子域。因此,`example.com`将被`.com`的所有者授予域名的所有权，而`blog.example.com`将被拥有`example.com`的任何人授予所有权。

虽然只看到一个子域是很常见的，但也有可能有一大堆。以下是完全有效的域名:

```
http://cs121.depalma.cs.gonzaga.edu 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过**资源记录**来管理域。

每个资源记录都是一个用于管理域的键值。如果您以前购买过域名，您可能会被要求设置一个`A`记录或一个`CNAME`记录。

一个`A`记录指向一个 IP 地址，一个`CNAME`充当其他记录的别名。

所以如果你想把`blog.foobang.com`重定向到`foobang.com`，你可能会得到这样的记录:

```
blog.foobang.com CNAME foobang.com
foobang.com A 192.168.2.2 
```

Enter fullscreen mode Exit fullscreen mode

如果您很好奇，可以用 unix 命令`dig`找到任何域的资源记录。因此，如果你使用的是 mac 或 linux，请在你的终端中尝试以下操作:

```
$ dig www.amazon.com 
```

Enter fullscreen mode Exit fullscreen mode

至少在 2018 年，你会看到有几个 CNAME 记录指向亚马逊的 CDN 和一个 A 名的版块:

```
;; ANSWER SECTION:
www.amazon.com.     1056    IN  CNAME   www.cdn.amazon.com.
www.cdn.amazon.com. 20  IN  CNAME   www.amazon.com.edgekey.net.
www.amazon.com.edgekey.net. 260 IN  CNAME   e15316.ci.akamaiedge.net.
e15316.ci.akamaiedge.net. 1 IN  A   23.74.61.104 
```

Enter fullscreen mode Exit fullscreen mode

# 前 7 个 TLD

制造这个系统的人首先是美国政府机构。没有人预料到阿帕网会像今天这样成为国际第一大互联网。

因此，前 7 个顶级域名*非常*以美国为中心:

```
com - commercial orgs like IBM
edu - universities like Berkeley
gov - the US government 
mil - the US military 
net - organizations providing network infrastructure
org - non-comercial organizations
int - international organizations like NATO 
```

Enter fullscreen mode Exit fullscreen mode

顶级域名不再仅仅是技术上的人工制品，它们有更多的含义。

因为 TLD 的所有权意味着网站的管理，顶级域名不仅仅是类别，**它们是文化的定义。**TLD 可以定义团队内外的规则。

然而，在这一点上，我们甚至没有国家代码。

# 拍得好，我们得去加国

国家意味着许多问题。作为程序员，我们希望事情整洁有序，但这并不符合现实世界。

宣布谁将获得 TLD 奖意味着技术专家被迫参与一些世界上最困难的地缘政治冲突。这不仅仅是时区，这是人类的统治。

那么，当我们往下看国家时，我们包括以色列和巴勒斯坦吗？台湾？库尔德斯坦？苏格兰？

是啊。ICANN(目前的管理机构)批准了所有这些地方的顶级域名。如果有足够大的群体支持它，它就会成为 TLD。

# 等等，这篇文章不是关于西班牙的吗？

实际上不是。是关于加泰罗尼亚的。

[![Catalonia](img/dfdb24ecbacbac3516aedfc066b028f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFeu0nGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u4r3osw23ptj0vtoe142.png)

加泰罗尼亚是西班牙一个独立冲突历史悠久的地区。像巴斯克地区一样，加泰罗尼亚说着不同的语言，并认为自己在文化上与周围的西班牙不同。

在 2005 年，`.cat`成为由 Fundació puntCAT 开发并由 ICANN 批准的专门针对加泰罗尼亚文化和语言的赞助顶级域名。

像许多文化顶级域名一样，除非你确实在做与加泰罗尼亚文化相关的事情，否则你无法获得`.cat`域名。

2017 年，该地区举行了可能违反西班牙宪法的独立公投；大多数国家的宪法不支持地区分裂。

作为回应，西班牙政府突袭了 puntCAT 的办公室，逮捕了其负责人，并关闭了多个。猫域名。

# 让我们记住我们在谈论什么

Fundació puntCat 在一个系统的数据库中维护线路，该系统旨在使加州大学洛杉矶分校的研究人员能够在 20 世纪 70 年代的计算机系统上与斯坦福大学公平竞争。

没人想到他们会参与千里之外的文化冲突。

然而，我们为防止研究人员相互绊倒而建立的技术，现在却成为历史强国之一的法律和文化冲突的一部分。

# 这样有什么意义呢？

首先，我们不会很快得到`.🐱`域名。

但更重要的是，技术决策的分量和影响可能远远超出你最初的准备。当你开始你的职业生涯时，不要对你的软件被使用的方式视而不见，如果它的大小是 100 倍，这意味着什么。

如果你知道你软件将被数十亿人使用，你会如何改变它？