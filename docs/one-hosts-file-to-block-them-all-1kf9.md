# 一个主机文件来阻止它们

> 原文：<https://dev.to/borisschapira/one-hosts-file-to-block-them-all-1kf9>

作为一个领域专家，人们经常问我如何提高网站的性能。有时，他们也问我如何在他们自己的机器上提高网页的性能，为了他们自己的浏览体验。在这种情况下，我的答案总是一样的:最容易摘到的果子肯定是 **hosts** 文件。

## 保持污垢远离

今天的网络充满了污垢。大多数网站都充斥着追踪器、广告和许多其他不利于网站加载的讨厌的东西。为了避免这一点，广告拦截器正在蓬勃发展。他们提供了一个快速而简单的解决方案(大多数情况下，是一个浏览器扩展)，可以阻止大多数不想要的内容。

不幸的是，对于 UX，广告拦截器不提供。它们通常会增加你的网络浏览器所使用的内存和 CPU 周期，减缓而不是提升你的浏览体验。有些做得比其他的好，整个浏览器 <sup id="fnref1">[1](#fn1)</sup> 都是基于屏蔽不想要的内容的想法构思出来的，并且做得令人难以置信。但是网络并不局限于你的浏览器，是吗？

您的计算机中的任何地方都需要 Web。大多数主流应用程序就像你的下一个媒体网站一样塞满了追踪器。有时候，你甚至可以看到你的 UI 中显示的广告(*你好，Skype。对，我说的就是你，你这个淘气鬼*。

广告拦截器对此无能为力。但是一个简单的 hosts 文件可以。

## 主持人……什么？

> 计算机文件 **hosts** 是一个将主机名映射到 IP 地址的操作系统文件。它是一个纯文本文件。
> <cite>[【主机(文件)】](https://en.wikipedia.org/wiki/Hosts_%28file%29)，维基百科上</cite>

每当您的浏览器与网站交互时，它实际上会请求一个通过其 IP 地址定位的服务器，如 185.31.40.11(IP v4)或 2a00:b6e0:1:20:2::1 (IPv6)。它非常类似于邮政地址。

现在考虑访问一个网页，比如 https://borisschapira.com。你的浏览器怎么知道联系哪个 IP 地址？嗯，浏览器只是简单地询问网络电话簿。至少，其中之一。被称为 <abbr title="Domain Name Server">DNS</abbr> 的电话簿为浏览器解析 IP 地址。

我不知道你，但是我从不使用电话簿。大多数时候，我已经在我的个人通讯录中收集了我想给其写信的人的地址。这是为您准备的**主机文件**。每当计算机上的一个进程需要访问 Internet 上的一个资源时，它首先通过 hosts 文件来查找在哪里可以找到它。

现在，假设您不希望这个进程在 badthings.com 域上找到资源。 *Easy peasy* ，通过将 badthings.com 域关联到一个像 0.0.0.0 这样的未指定的地址，把它扔到错误的轨道上。

因此，基本上，如果有人列出了所有发生不好事情的域，我们可以在我们的 hosts 文件中将它们全部重定向到 0.0.0.0，并使**成为我们自己的 Web** 一个更酷的地方。

## 一个项目把他们全部聚集起来

我在这里描述的没有什么新的东西。酷人们这么做已经很多年了，分享他们的主机文件。假新闻网站、游戏平台、色情中心、加密页面、欺诈企图和骗局都被耐心地识别出来，并在开放访问文件中列出。

我使用 Steven Black 的“hosts”项目(python 脚本)将它们塞进包含超过 70k 个域名的 2MB 主机文件(T3)中(T2)。如果你想知道，我可以用我自己的白名单(否则阻止我不想阻止其请求的域)和重定向(这允许我写这篇文章并在 https://borisschapira-dev.com:10443/的[上检查结果，它实际上指向我自己的机器)来改变结果。](https://borisschapira-dev.com:10443/)

如果你不熟悉命令行并使用 Windows 10，那么 [hostsman](http://www.abelhadigital.com/hostsman/) app 将帮助你达到同样的目的(*不幸的是，正如我刚刚发表的帖子一样，网站看起来向下。好在[有便携版](https://portapps.github.io/app/hostsman-portable/)* )存在。在 2015 年(FR) 之前，我不会建议在以前版本的 Windows 上修改你的主机文件，即使[我这么做了，除非你喜欢每 30 分钟运行一次`ipconfig /flushdns`。在 Linux 上，我听到人们谈论使用](https://borisschapira.com/2015/08/de-windows-a-mac/) [dnscrypt-proxy](https://github.com/jedisct1/dnscrypt-proxy) …但是我自己从来没有试过。

<figure>

[![A screencapture from hostsman sources manager](img/e96c3dbede7ef49d8e40c0fbc98b318b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--32KyrrU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xp1xk5w8trkjx6cxblqt.png)

<figcaption>You can manage Update Sources in hostsman</figcaption>

</figure>

## 来自我日常生活的一些小贴士

有时，我需要暂时禁用这个主机文件。例如，当我需要帮助 [Dareboost](https://www.dareboost.com/) 客户理解第三方脚本对他们页面的影响时(因此，我需要自己加载这些 3p)。

为了启用或禁用我的 hosts 文件，我使用了我开发的命令行函数，并将其放入我的`~/.profile`文件 <sup id="fnref2">[2](#fn2)</sup> :

```
function togglehost() {
    if [ -e /etc/host.bak ]
    then sudo mv /etc/host.bak /etc/hosts
        echo "Hosts file is active again"
    else sudo mv /etc/hosts /etc/host.bak
        echo "Hosts file is set aside"
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦加载了这个函数，激活或停用我的 hosts 文件就非常简单:

```
$  togglehost
Hosts file is set aside $  togglehost
Hosts file is active again 
```

Enter fullscreen mode Exit fullscreen mode

但由于我有时会忘记把它放回原处，所以我设置了一个例程，每分钟检查文件是否在适当的位置，如果不在，就通过使用雅伊梅·皮亚的 [noti](https://github.com/variadico/noti) 触发通知来警告我。为了执行这个常规检查，我使用了`crontab`。

```
* * * * * if [ ! -e /etc/hosts ]; then /usr/local/bin/noti -t "Host file" -m "does not exists"; fi 
```

Enter fullscreen mode Exit fullscreen mode

我已经使用这种技术很多年了，有时候，我忘记了我的 hosts 文件对我有很大的保护。每当我需要使用别人的计算机，或者临时禁用我的 hosts 文件时，我意识到它给我带来的舒适程度。

我很清楚调整它自己的 hosts 文件是一个很好的技术解决方案。它并没有完全取代广告拦截器(或者我还没有聚合足够的文件)，但它的性能令人难以置信，我强烈建议日常浏览时使用。

你自己试试，然后告诉我你的想法！

* * *

## 总结

主机文件是您计算机内部的地址簿，它将 Web 请求引导到正确的服务器。用指向虚无的域填充您的 hosts 文件，这些请求很快就会失败。多年来，人们一直在共享他们的主机文件。现在有办法把它们连接起来，并且众包了一个解决我们每天都要面对的肮脏网络的方法。

* * *

1.  如果你从未测试过勇敢的浏览器，我只能鼓励你这样做，并加入 400 万信任它的人来修复网络。 [↩](#fnref1)

2.  实际上，我把这个函数放在我的`~/.zprofile`里，就像我用[我的 ZSH](https://ohmyz.sh/) 一样。 [↩](#fnref2)