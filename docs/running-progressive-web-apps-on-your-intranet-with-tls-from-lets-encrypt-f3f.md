# 使用来自 let 加密的 TLS 在您的内部网中运行渐进式 web 应用程序

> 原文：<https://dev.to/justinribeiro/running-progressive-web-apps-on-your-intranet-with-tls-from-lets-encrypt-f3f>

在这个世界上，每个 web 应用程序或服务似乎都是一个 cli 命令，无法通过您选择的云提供商在全球范围内无限扩展，我们有时会忘记，并不是所有的东西都希望或能够存在于云中。鉴于我多年来不缺乏内部和外部的沟通和样本，我没有忘记我做出这一声明的讽刺意味。

当我们想到 web 时，我们通常会想到外部的、全球性的、我可以去任何地方的 web，忽略了隐藏在内部网络(还记得内部网)上的大量 web 应用程序，它们默默无闻地为人们做着自己的工作。在与许多这样的开发人员交谈后，我经常听到“我想使用渐进式 Web 应用程序特性，但我们无法在内部支持它”的悲伤故事

这可能意味着很多事情，并且我决不会涵盖所有组织的内部动态。然而，我在许多中型公司中最常听到的说法是，他们无法满足 TLS 的要求。“我们不运行内部 CA”或“没有人知道如何处理证书”，最后是“警告消息吓坏了我们的用户(例如，不匹配警告)。”

TLS 很重要；没有它，服务人员尤其无法操作，我们会失去后台同步、推送通知和添加到主屏幕操作。我们缺乏控制。

如何弥合差距？让我们来加密、DNS 和一个我们可以自动化的域服务。

## 让 DNS 为我们服务

DNS 是互联网的电话簿。电话簿是电话公司用来放在你家的东西，这样你就可以通过名字查找人们，找到电话号码。当我们输入一个域名时，比如说 justinribeiro.com，我们实际上被路由到一个 IP 地址，如`dig`所示:

[![Output from dig command on justinribeiro.com](img/3d924572f7b0fb3a774ed4f3d4061eb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oqWFISQG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/20181018-dig-external.png)

在互联网上，我们需要唯一的 IP 地址来真正解决问题(让我们暂时忽略 SNI)。有趣的是，一个内部的、非公共可路由的 IP 地址可以设置在 A 记录上。在这种情况下，我们用`dig`来看看 checkcheck.ribeiro.house:

[![Output from dig command on checkcheck.ribeiro.house](img/7ff7dc31a39c88b6d9a26f78ffc57a64.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RAisvLAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/20181018-dig-internal.png)

看起来很奇怪，不是吗？不是 192。<sup>168</sup><sub>16</sub>前缀[覆盖在 RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 作为私有地址空间？的确，你是对的。问题是，DNS 不在乎。它只是告诉我们应该去哪里，即使你不能在外面开放的互联网上到达那里。然而，对于我们网络上的内部用户来说，这是一个可路由的域名。现在我们只需要一个证书。

## 让我们加密救援吧

我有时会看到这样的问题“为什么我不能使用 Let's Encrypt 为我的设备/服务器生成 intranet 证书？”总的来说，当你退一步来看，这并没有太大的意义；Let's Encrypt 是一个公共认证机构，通常通过验证有效的 DNS、可路由的 DNS 名称来运行。

在过去，你必须至少暂时让你的站点可用，以进行适当的验证。对于大多数人来说，从内部网的角度来看，这是不可行的。但是有了 DNS-01 挑战，我们不需要网站有全球可路由访问。我们只需要一个在外面的 DNS 提供商，我们可以用一个挑战记录更新。

这对我们的内部 PWA 非常有效，允许我们为我们选择的域生成一个证书，即使它只在内部路由。

让我们看一个例子。假设我的域名托管在谷歌的云域名系统上，我使用的是流行灵活的 acme 客户端[脱水](https://github.com/lukas2511/dehydrated)。我们可以安装 [Google Cloud hook](https://github.com/spfguru/dehydrated4googlecloud) ，假设我们在`gcloud` cli 中登录了正确的帐户，我们可以为我们的域生成证书，并安装在我们的内部 web 服务器上，让用户通过所选的域访问它:

[![concatenated output from running dehydrated command](img/63c2adfad3f8a9586823b84a0a8e26f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jAeee0JF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/20181018-acme-cert-gen.jpg)

突然之间，我们不仅获得了进步的 web 特性，还在内部获得了额外的安全层(这是我在许多公司的经验中经常缺乏的)。对于任何数量的附加端点，您都可以这样做。

## 永远更 perf

现在你可能会说“Justin，我仍然不知道如何设置脚本来运行更新，我仍然不知道如何管理它，我们的旧 web 服务器被 Netscape 卖给了我们。”我听到了，你想要更多的性能。关心的人，见见[球童](https://caddyserver.com)。

从 H2 和 QUIC 支持到 automagic TLS，Caddy 有许多真正使渐进式网络应用程序成为现实的功能。automagic TLS 与 DNS-01 challenge 和一些 [DNS 提供商](https://caddyserver.com/docs/tls.dns.googlecloud)一起工作，与上面的设置不同，坦率地说，这是您可能会看到的 TLS 证书安装:

[![Caddy running with little more than a config file serving on TLS.](img/22331c9f5e662fb4d5b286d8a543192d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KUDgBCjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/20181018-caddy.png)

渐进式网络应用程序。内部网。TLS。速度。

## 你不能这样做

这是我解释反对这种方法的部分。

1.  你正在向潜在的攻击者泄露关于你内部网络的信息。完全同意，您透露了您在内部运行的内容，包括可能的名称选择和内部 IP。你应该权衡这对你的组织意味着什么。

2.  运行您自己的内部网 CA，并像 real ops 一样将证书推送给客户。再次重申，不否认这是一种选择。在许多拥有专业知识和基础设施的大型组织中，这是一种非常现实的方法。然而，对于许多组织来说，他们的后兜里并没有这些东西。

3.  在云中这样做太便宜了。可能是，肯定是。对于一些人来说，这不是一个选项(我的 a/v 接收器不在互联网上)。

4.  仅仅是进步的网络功能似乎就需要做很多工作。操作系统更新和安全补丁也是如此，但你做那些(对吗？对吗？？？请做那些。请)。

5.  我的内部网应用不需要安全性。这个让我很难过。在运行应用程序时，安全性不应该是可有可无的，不管它是不是 web。

6.  这是一种古老的技术，消费者路由器/防火墙在设置时会使用这种技术。还是那句话，不自称我是第一，只是希望帮助开发者和他们的用户发挥潜力。

## 在家试试吧！

毫无疑问，我已经为公司推出了这款产品，没有任何问题。我也在家里运行这种设置(albiet 和一些额外的层在这篇文章的主题之外)。我的家庭网络上有相当多的设备，其中许多都有一次性的 Android 或 iOS 应用程序，自 2012 年以来就没有更新过。这些设备不存在于互联网上，我也不想让它们存在于互联网上(哦，那将是一场大屠杀)。

我确实想控制它们，最好是用一个进步的 web 应用程序，我网络上的任何人都可以在闲暇时打开它(是的，我是一个信任别人的人)。因此，这些年来，我写了各种各样的 PWA 小程序来控制这些设备，取得了相当大的成功。

因此，如果你感觉像“嘿，我有这个备用的英特尔 NUC，迫切需要从这个 wifi 连接的应用程序逆向工程我的孩子玩具使用的协议”，那么旋转一个域，给自己一个证书，并加入到渐进的网络应用程序革命中来。