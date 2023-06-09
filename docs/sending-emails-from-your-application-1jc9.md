# 从您的应用程序发送电子邮件

> 原文：<https://dev.to/elmuerte/sending-emails-from-your-application-1jc9>

当你开发一个 web 应用程序时，你无法回避这样一个事实:你需要给你的用户发送电子邮件。最基本的用例是发送与帐户相关的电子邮件(如密码重置)。

发送电子邮件并不是你的申请中最复杂的部分。然而，很多人犯了很多错误。一旦你想使用漂亮的 HTML 电子邮件而不是`text/plain`电子邮件，错误通常就开始发生了。

[![](img/4fcc8b4505884bb2672ff31447239b70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jeXOJ-KR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tasb55i28qp5yaii0yf3.png)

# HTML 喜欢它是 1999 年

创建 HTML 电子邮件不是一项简单的任务。这不是一个简单的设计一个好看的 HTML 页面的问题，它已经完成了。所有的电子邮件客户端对于 HTML 和 CSS 的使用都有不同的规则。

最好的方法是像 1999 年那样设计一个 HTML 页面:

*   `<table>`用于定位
*   基本内嵌样式

现在你只需要在人们使用的所有不同的电子邮件客户端中测试你的电子邮件:雷鸟、Outlook、Outlook live、Lotus Notes。iOS 邮件 app，GMail，GMail app 等。

# 工具

幸运的是，现在已经不是 1999 年了，人们已经发明了一些工具来简化 HTML 邮件的制作。

本文的目的不是提供可用工具和服务的完整列表。更详细的列表请查看乔纳森·迪翁的精彩邮件 。

## 验证器和预览器

真的有很多不同的支持 HTML 的电子邮件客户端。它们都以不同的方式支持 HTML。有时，使用的设备也会改变电子邮件的显示方式。

在不同的客户端上验证结果并不是一项简单的任务，因此这些服务中的大多数都是付费的。

*   [邮件上酸](https://www.emailonacid.com/email-testing)
*   [HTML 邮件检查](https://www.htmlemailcheck.com/)
*   [收件箱检查器](http://www.inboxinspector.com/)
*   [石蕊](https://litmus.com/)
*   [石蕊试纸](https://putsmail.com/)

## 内联者

处理内联样式是一件令人头疼的事情。这是发明 CSS 的主要原因之一，这样你就可以很容易地改变样式，并将其应用于所有适用的元素。

如果你正在开发自己的 HTML 电子邮件，你可以简单地用 CSS 设计 HTML，然后使用一个样式内嵌器来应用它。

有几个内联器可用，或者作为在线服务，或者作为下面列出的框架的一部分。

仅举几个例子:

*   [石蕊试纸](https://putsmail.com/inliner)
*   [MailChimp](https://templates.mailchimp.com/resources/inline-css/)
*   [NodeJS CSS 内嵌器](https://www.npmjs.com/package/css-inliner)
*   [火炬箱直列式探针](https://inlinestyler.torchbox.com/)
*   [Zurb 的电子邮件基础](https://foundation.zurb.com/emails/inliner-v2.html)

由 [Mosaico](https://mosaico.io/email-client-tricks/email-inliners/) 对各种内嵌器进行了比较。

## 框架

除了推出自己的设计，你还可以简单地采用一个框架或使用一个现有的模板。如果你只是做一些小的样式调整和添加内容，你可能不需要使用付费服务来验证不同客户端的结果。

*   地狱犬
*   [邮件基础](https://foundation.zurb.com/emails.html)
*   [MJML](https://mjml.io/)
*   [松木](https://thememountain.github.io/pine/)

[![](img/3a4054fd671a289ef51e2321c8440942.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SzRHZhCa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i01fnjk1qwuw5egyvz9o.jpg)

# 故障

我开始这篇文章并不是为了讨论所有可以用来创建 HTML 电子邮件的工具。它是关于人们在发送电子邮件时做错的各种事情。

即使使用上述工具，这些故障仍然会发生。

## 收录的图片

在 HTML 电子邮件中包含图像有两种方式:

1.  附上
2.  外部链接

如果 HTML 邮件还不够大，你可以通过附加图片让它们变得更大。如果你一定要包含图片，使用所有的技巧使图片尽可能的小。同样，HTML 就像 1999 年一样，假设用户使用 56k 调制解调器连接。

链接到外部图像不是一个合适的选择。大多数电子邮件客户端不会加载外部图片，原因很充分:*隐私*。

从外部资源加载图像会暴露阅读器。它可以用来验证用户是否真的阅读了电子邮件。这是相当不可靠的，因为许多基于网络的电子邮件客户端在需要时通过代理检索图像。

[![](img/40c0389a3913199643b4c83db608b4a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vgIjuhuq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qb2s27h1akn96yf2j587.png)

## 缺失图像

如上所述，外部图像通常不会呈现给用户。而且附加的图像并不总是被显示。

电子邮件客户端，尤其是移动电话上的客户端，通常不下载完整的电子邮件，而只下载重要部分(即文本内容)。附加的图像可以下载进一步的要求。

你应该假设你的邮件是不带图片的。如果图像纯粹是装饰性的，考虑将它们全部移除。否则，请确保始终为图像包含适当的`alt=""`值。如果图像不需要文本替换，则使用空的 alt 值，以便不显示损坏的图像占位符。

在不显示图像的情况下，电子邮件客户端的行为会有所不同。有些会显示 alt 值。其他人只是不显示任何东西，直到图像被激活。如果图像显示电子邮件的重要信息。考虑在图片下方用小字体添加简短描述，这样读者就知道某些内容没有显示。

## 长网址

谁不喜欢真正长的网址？我认为大多数人不喜欢它们。然而，由于某种原因，电子邮件通常包含很长的网址。通常，URL 的很大一部分包含一些编码数据来跟踪读者。

长 URL 的问题是不容易验证。它们不容易复制。他们简直不友好。

你希望你的应用程序中有好看的 URL，为什么不在你的应用程序创建的电子邮件中呢？当生成这些 URL 时，尽量保持它们尽可能的短。如果您的应用程序已经有一个数据库，并且您需要用 URL 来跟踪大量数据，那么就在您的应用程序中缩短它。将所有数据存储在一个表中，只需在 URL 中传递一个简短的引用。

什么更好看？

```
https://www.example.org/mailcmpgn/xmpl/p/TXVsZG9vbjogW0p1c3QgYmVmb3JlIGhlIGdldHMgYXR0YWNrZWQgYnkgYSByYXB0b3JdIENsZXZlciBnaXJsLiA
https://www.example.org/mailcmpgn/xmpl/p/bjogW0p1c3 
```

Enter fullscreen mode Exit fullscreen mode

或者，你可以使用外部网址缩写。但是要注意隐私、安全性和可靠性的含义。

## 可怕的`text/plain`替代品

向不理解 HTML 的接收者发送 HTML 并没有真正的帮助，因为他们可能看不懂。MIME 标准的一部分是`multipart/alternative`结构，它允许您提供相同内容的可选表示。通过这种方式，电子邮件客户端可以选择最佳替代方案呈现给用户。

即使电子邮件能够呈现 HTML 电子邮件，包含`text/plain`选项也有好处。

下面是一封邮件的 MIME 树。

```
[multipart/alternative, 7bit, 63K]
├─> [text/plain, quoted, utf-8, 6.7K]
└─> [text/html, quoted, utf-8, 56K] 
```

Enter fullscreen mode Exit fullscreen mode

HTML 版本的大小大约是文本版本的 9 倍。通过 IMAP，电子邮件客户端可以简单地请求`text/plain`版本并显示它以节省带宽/时间。

如今，大多数电子邮件客户端都支持 HTML(甚至像 Mutt 这样基于文本的电子邮件客户端)，带宽也不是大问题。因此，发送一个`text/plain`备选方案主要是出于礼貌。

除了有很多应用程序确实会发送像
一样带有绝对无用内容的`text/plain`电子邮件

```
Your browser does not support HTML. 
```

Enter fullscreen mode Exit fullscreen mode

(是的，它说的是浏览器，不是邮件客户端)

```
Please view this email in your browser. 
```

Enter fullscreen mode Exit fullscreen mode

(但不包括网址)

还有一些带有`text/plain`选项的电子邮件确实提供了在线查看电子邮件的 URL。但是

1.  相当长的网址，不容易复制
2.  在网上观看这是一个隐私和安全问题

如果发送应用程序只包含 HTML 版本，那么在电子邮件客户端的当前状态下，将会创建更好的用户体验。

创建一个`text/html`的`text/plain`版本并不困难。有很多工具可以实现这一点(下面将详细介绍)。当你把 HTML 转换成纯文本时，你必须确保输出中包含 URL。

这不是一封有用的邮件:

```
Please click here to change your password. 
```

Enter fullscreen mode Exit fullscreen mode

这个比较好用:

```
Please click here[1] to change your password.

[1] https://example.org/user/password 
```

Enter fullscreen mode Exit fullscreen mode

# 避免失败

要创建最好的 HTML 电子邮件，请遵循以下准则:

*   使用 HTML 电子邮件框架
*   形象
    *   尽可能避免
    *   尽可能小的连接
    *   始终使用`alt=""`
*   使用简短友好的 URL
*   纯文本替代
    *   创造一个合适的选择
    *   或者根本没有

或者坚持使用不太漂亮但简单的基于文本的电子邮件。

[![](img/4e4291775dee10a9201687ded121957f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RLze3oEH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uvdsjayrgj0rpi4q6lxr.jpg)

# HTML 转文本

你可以为你的 HTML 邮件创建一个显式的纯文本替代。但是这样你就必须维护两个版本的电子邮件模板。将 HTML 结果转换成纯文本更容易。

一种方法是去掉所有 HTML 标签，只显示元素之间的文本。这可以用一个简单的正则表达式来完成。但是这不能提供有用的最终结果。

有两件事非常重要:

1.  可见 URL
2.  可读性

HTML 中包含的所有链接都应该将它们的 URL 包含在结果文本中。否则读者不知道去哪里。

仅仅去掉 HTML 标签会产生一堵文本墙。转换器 HTML 应该至少包含段落之间的空行。

```
 [663399]

   Sidebar

Hello, Han Fastolfe

   Lorem ipsum dolor sit amet, consectetur adipisicing elit.
   Laboriosam nobis velit, aliquid pariatur at fugit. Omnis at
   quae, libero iusto quisquam animi blanditiis neque, alias
   minima corporis, ab in explicabo? 
```

Enter fullscreen mode Exit fullscreen mode

## 外壳工具

如果你在一个类似 UNIX 的系统上，并且你的应用程序可以执行 shell 命令，你可以很容易地使用 lynx 将你的 HTML 转换成一个合适的文本版本。

```
lynx -force_html -hiddenlinks=ignore -width=76 -dump /tmp/myfile.html 
```

Enter fullscreen mode Exit fullscreen mode

电子邮件的[“侧边栏”示例](https://foundation.zurb.com/emails/templates/sidebar.html)产生以下
[结果](https://gist.githubusercontent.com/elmuerte/3718a312866692e74a8f7d186c09ec49/raw/76917e0f24b34dd8b0c92fd890605bfbf23c774d/gistfile1.txt)。
请注意，lynx 不会对 URL 执行重复数据删除。

或者使用链接

```
links -html-numbered-links 1 -width 76 -dump /tmp/myfile.html 
```

Enter fullscreen mode Exit fullscreen mode

## Java

使用 [Jericho](http://jericho.htmlparser.net) 库，将 HTML 转换成文本
也很简单

```
String renderedText=new Source(yourHtml).getRenderer().toString(); 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Jericho 会将 URL 写在链接旁边，比如:

```
my link<http://example.org> 
```

Enter fullscreen mode Exit fullscreen mode

这会降低可读性，尤其是当链接很长时。使用稍微修改的渲染器，输出产生类似 Lynx 的引用