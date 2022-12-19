# 您可能需要一个 Web 应用程序安全扫描器——但是是哪一个呢？

> 原文：<https://dev.to/robertsvensson/you-might-need-a-web-application-security-scanner-but-whichone-16ii>

如果你有一个网站，那么你的网站很可能容易受到各种攻击。你的网站越大越复杂，就越难跟踪潜在的安全问题。

这就是为什么你需要一个 Web 应用安全扫描器，或者仅仅是一个扫描器。乍一看，扫描仪的工作很容易。它会扫描您的 web 应用程序，并生成一份报告，突出显示它发现的安全问题，以便您可以缓解这些问题。

问题是，由于定制的 web 应用程序可以以一百万种不同的方式组合在一起，用扫描仪发现安全问题并不总是可靠的。有时，扫描器会发现并报告您的 web 应用程序中的漏洞，有时则不会。

那么，市面上的一些扫描仪到底有多好呢？你怎么知道你的钱花得值不值呢？

为了找到答案，我创建了一个有三大安全缺陷的 web 应用程序。理想情况下，这些缺陷应该被所有扫描仪检测并报告。

web 应用程序的三个缺陷是:

#### 一-敏感信息作为源代码注释:

`<!--the username for our most sensitive data is Summer2018 with the password MegaSecret-->`

#### 双敏感数据曝光:

根文件夹中有一个名为 passwd 的文件，内容如下:
`tom:x:1000:1000:Vivek Gite:/home/vivek:/bin/bash`

#### 三码注入:

该网站有一个表单，用户可以在其中填写自己的电子邮件地址，然后按“提交”,电子邮件地址将出现在“提交”按钮下方，如下所示:

[![Unfiltered input](../Images/6eed18b2af84863d00cc34351c901f63.png "Unfiltered input")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HT-NDNrT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AHSct9oJ4QAxF4wHE)

问题是用户输入没有被净化。因此，如果有人输入

# `Hello!!!`

而不是他们的电子邮件地址，我们将得到如下呈现的 HTML:

[![Unfiltered input h1](../Images/a7edd444792afbf574fc1e17cc12d418.png "Unfiltered input h1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ivmqa-k---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AAj3Fr42iWKeT6DpS)

也有可能插入 JavaScript 和其他各种令人不快的东西。

### 迎接扫描器

**Patronus.io** :他们自称是一个——“为你的网站提供自动化安全解决方案”。对我来说听起来不错，但是让我们看看它是不是真的，好吗？

**Detectify** :说他们“扫描你的网站，寻找由 150 多名白帽黑客众包的安全问题”。听起来很地下，很酷，不是吗？但这都是空谈吗？

**成立**:-它们“……安全、准确、自动地扫描您的 web 应用程序，提供漏洞的深度可见性和有价值的上下文，以确定补救的优先顺序”。太好了，但是它有用吗？

### 第一轮——发现敏感信息作为源代码注释

否-守护神 io
是-检测
不成立

### 第二轮——发现敏感数据暴露

否-守护神 io
否-检测
是-有效

### 第三轮——发现代码注入

否-守护神 io
是-检测
不成立

## 那么谁赢了？

### 胜利者

到目前为止，检测扫描花费的时间最长。但是等待是值得的。Detectify 设法找到了代码注入漏洞和包含用户名和密码的源代码注释。Detectify 未能找到密码文件，但我可以忍受这个短密码

### 亚军

Tenable 在根目录中找到 passwd 文件没有问题。然而，代码注入漏洞就像 HTML 源代码中暴露的用户名和密码一样不为人所知

### 最后死去

另一方面，守护神是令人惊讶的星期。他们的扫描仪没有发现这三个漏洞中的任何一个。我建议你把钱花在别的地方，因为这种产品只会给你一种虚假的安全感。如此脆弱的安全工具应该被完全禁止——我们应该得到更好的。

### 结论

自动扫描仪不是很好。当然，他们可以抓到一两个低垂的果实——但是他们仍然落后于手工渗透测试。因此，扫描仪应该只被视为您的安全难题的一小部分。因为如果你仅仅依靠扫描器来保证你的 web 应用程序的安全，你可能会大吃一惊。