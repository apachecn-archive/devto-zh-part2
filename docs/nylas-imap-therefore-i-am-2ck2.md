# IMAP，故我在。

> 原文：<https://dev.to/nylas/nylas-imap-therefore-i-am-2ck2>

[![Nylas: IMAP, Therefore I Am.](img/2a45954f399b3d132e59a39385a555ec.png)T2】](https://www.nylas.com/blog/nylas-imap-therefore-i-am)

在本帖中，我们将深入探讨 IMAP。IMAP 代表 Internet 邮件访问协议，它是描述如何访问电子邮件邮箱中的邮件的开放标准。虽然 IMAP 是接收电子邮件的重要部分，但它并不总是最容易实现(或理解)的，这就是 Nylas 存在的原因！简而言之，IMAP 是 Mail.app、Thunderbird 和 Mailspring 等电子邮件客户端用来从您的电子邮件帐户下载邮件并进行更改(如归档邮件或将其分类到文件夹中)的协议。

## IMAP 简史

IMAP 最初是由斯坦福大学的 Mark Crispin 于 20 世纪 80 年代创建的。Crispin 后来去了华盛顿大学，在那里花了 20 年时间研究 IMAP 规格和参考实现。IMAP 规范以“征求意见稿”或 RFC 的形式出现，RFC 基本上是一个备忘录，描述如何实现已经被互联网工程任务组采纳为标准的协议。RFC 可能会被修订以进行澄清或更改，描述 IMAP 的当前 RFC 是 2003 年发布的 [RFC3501](https://tools.ietf.org/html/rfc3501.html) 。没错:大多数邮件客户端用来同步邮件的协议已经超过 15 年了！

IMAP 最初是作为一种更好的替代协议而设计的，它的目标是“[允许像在本地一样操作远程邮箱](https://www.washington.edu/imap/papers/imap.vs.pop.brief.html)”—具体来说，POP 的操作模式是将邮件下载到您的本地机器上，然后从服务器上删除，如果您使用多台计算机，这就很难管理您的邮件。这在分时主机时代不成问题，但随着个人电脑变得越来越普遍，你可能在家里和办公室都有一台电脑，并想从任何一个地方浏览你的电子邮件。

IMAP 的原始版本已经失传，IMAP 2(1988 年发布)是第一个被纳入标准的版本。IMAP 的第一个版本只支持“在线”操作:它假设在查看或修改邮件时，您的邮件客户端会连接到服务器。您不能使用它在本地同步您的邮箱副本，然后在重新连接时更新您的本地副本，因为邮件仅由一个“序列号”来标识，而序列号在客户端会话之间并不持久。人们只能想象，在那些日子里，人们认为，无论何时你想查看电子邮件，你都必须有一个可靠的网络连接。我的猜测是，这是因为从大型机和分时系统进行计算本质上是直接登录到服务器上，这意味着您可以随时连接互联网。

快进到拨号上网时代的几年，很明显，不停地占用你的电话线以便你能阅读你的电子邮件并不能解决问题。输入对“断开连接”操作的支持，这是随着 1994 年引入的 [IMAP4](https://tools.ietf.org/html/rfc1730) 而附加到 IMAP 上的。该协议的这次修订增加了持久消息标识符(称为“uid”)和整个[草案规范](https://tools.ietf.org/html/draft-ietf-imap-disc-00)，解释了如何使用 IMAP 命令将本地缓存数据的更改同步到服务器上存储的数据。我们稍后会详细讨论这一点，但是协议随时间的演变是实现 IMAP 客户端在当今时代变得复杂的主要原因，因为断开操作是现代应用程序的一个必需特性。

自 1996 年以来，基本的 IMAP 协议一直保持不变，此后所有的新功能都在该协议的可选扩展中实现，其中一些已经成为标准，但即使是最常见的扩展的采用仍然在电子邮件服务提供商之间存在很大差异。

## IMAP 如何工作

现在您已经了解了该协议的一些历史，我们将交互式地向您介绍其工作原理的具体细节。我们将连接到一个电子邮件帐户，列出邮箱中所有可用的文件夹，然后下载一封邮件。在这个过程中，我们将识别和解释协议中需要理解的相关部分。

只要你能使用终端、`openssl`命令行工具和支持 IMAP(包括 Gmail)的电子邮件账户，你就能跟随我们完成协议。

### TCP，HTTP & IMAP

许多现代客户端-服务器协议发生在 web 浏览器和服务器之间，或者 app 和 API 之间。这些 API 都是在基本协议 HTTP(“超文本传输协议”)之上实现的，HTTP 定义了不同类型请求的语义—GET、PUT、POST 等。HTTP 是在称为 TCP(“传输控制协议”)的较低层协议之上实现的，它只是保证信息包被可靠地传送到目的地。

像 HTTP 一样，IMAP 是在 TCP 之上实现的，它定义了不同类型请求的语义，称为 IMAP 命令。一般来说，IMAP 比 HTTP 简洁得多，HTTP 在 90 年代早期是 IMAP 的关键，因为网络连接的带宽非常低。

## 步步 IMAP

我们开始吧！在家里的终端上输入命令，在相关的地方输入你的电子邮件账户的详细信息。请注意，如果您使用的是 Gmail，您可能需要[禁用针对“不太安全的应用程序”登录的保护](https://support.google.com/accounts/answer/6010255?hl=en),这样才能工作。

任何客户端需要做的第一件事就是在特定端口上连接到远程服务器。这些端口可能会有所不同，因为邮件服务器最初接受明文连接，后来又增加了对安全加密连接的支持。您通常可以通过查看提供商的帮助文章来找到他们的设置。例如，[这里是](https://support.google.com/mail/answer/7126229?hl=en)在哪里可以找到 Gmail 的设置。(如果你对为什么使用不同端口的背景很好奇，我们推荐[这本来自 Fastmail 的神奇指南](https://www.fastmail.com/help/technical/ssltlsstarttls.html)。)

设置好安全连接了吗？我们走吧！

在命令行上，我们可以使用`openssl`命令行实用程序进行连接。我们必须使用`openssl`而不是`telnet`，因为否则我们可能会以明文形式在网络上发送敏感数据，如密码和私人电子邮件，从而使它们对攻击者的网络监听敞开大门。`openssl`在继续操作之前，通过建立安全连接并验证远程服务器的证书来保护我们的连接。

```
 $ openssl s_client -connect imap.gmail.com:993 -crlf
    [... certificate verification removed for brevity ...]
    * OK Gimap ready for requests from 216.38.147.18 h1mb556258870pjq 
```

Enter fullscreen mode Exit fullscreen mode

在上面的命令中，`s_client`表示我们正在调用`openssl`的功能作为基本的 SSL/TLS 客户端，`-connect`指定要连接的主机名和端口，当您在终端中按下“Enter”时，`-crlf`将转换为 IMAP 服务器期望的字符，以正确结束一行。连接后，`openssl`会检查服务器上的 SSL/TLS 证书，以确保我们的连接没有被劫持，IMAP 服务器会显示一条问候语，说明它已准备好接收来自客户端的请求。

### 命令

IMAP 命令通常如下所示:

[...]

我们将在名为 CAPABILITY 的示例命令的上下文中解释这些部分。功能允许你询问服务器它支持哪些 IMAP 扩展:

```
 tag1 CAPABILITY
    * CAPABILITY IMAP4rev1 UNSELECT IDLE NAMESPACE QUOTA ID XLIST CHILDREN X-GM-EXT-1 XYZZY SASL-IR AUTH=XOAUTH2 AUTH=PLAIN AUTH=PLAIN-CLIENTTOKEN AUTH=OAUTHBEARER AUTH=XOAUTH
    tag1 OK Thats all she wrote! h1mb556258870pjq 
```

Enter fullscreen mode Exit fullscreen mode

哇哦，我们刚刚和一个 IMAP 服务器通信了！让我们解释一下我们发送了什么，我们得到了什么。

命令类似于 HTTP 中的请求。它是告诉服务器做某事或向它询问信息。在这种情况下，我们要求服务器告诉我们它支持哪些功能。稍后将详细介绍功能。

CAPABILITY 命令没有参数，但如果有，我们只需在命令名之后，按 enter 键之前输入参数。

### 标注

标签(在本例中为`tag1`)由客户端生成，服务器在命令响应的最后一行发送回标签。标签可以包含任何字母数字字符，甚至一些符号，不需要包含一个升序的整数——尽管这是一种生成唯一标签的便捷方式。以`*`开头的服务器响应被称为无标记响应，这意味着它们不代表客户端请求的命令已经完成。在这种情况下，未标记的响应是能力列表，标记的响应是命令的`OK`状态和服务器程序员留下的有趣消息。

这种标记能力意味着服务器可以在同一连接上同时处理来自客户端的多个请求，并通过发回适当的标记来指示请求的完成。实际上，许多客户机不支持发送并发请求的能力，只是在命令发送后阻塞等待数据到达打开的套接字。

### 能力

那么，这个“能力”列表到底意味着什么呢？“功能”是服务器支持的功能的简称。其中一些功能，如`AUTH=PLAIN`，包含在基本的 IMAP4rev1 规范中，其他的通过扩展来实现。扩展允许将新命令添加到 IMAP 协议中，而不需要新版本的协议规范。登录之前，服务器上支持的功能列表将与登录之后的功能不同，因为列出与注销或登录状态无关的功能几乎没有用处。

### 认证

以下是如何向 IMAP 服务器鉴定您的邮件帐户:

```
tag2 LOGIN spang@nylas.com <super-s3krit-password>
tag2 NO [AUTHENTICATIONFAILED] Invalid credentials (Failure) 
```

Enter fullscreen mode Exit fullscreen mode

### 服务器响应

如你所见，服务器响应并不总是说`OK`。以下是允许服务器响应命令的快速指南:

`OK` =成功！

`NO` =失败！

`BAD` =我听不懂你说什么！

如果需要的话，服务器可以在响应后添加更多的信息，比如`[AUTHENTICATIONFAILED] Invalid Credentials (Failure)`。

```
tag3 LOGIN spang@nylas.com <super-s3krit-password>
* CAPABILITY IMAP4rev1 UNSELECT IDLE NAMESPACE QUOTA ID XLIST CHILDREN X-GM-EXT-1 UIDPLUS COMPRESS=DEFLATE ENABLE MOVE CONDSTORE ESEARCH UTF8=ACCEPT LIST-EXTENDED LIST-STATUS LITERAL- SPECIAL-USE APPENDLIMIT=35651584
tag3 OK spang@nylas.com authenticated (Success) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在成功登录后，服务器再次向客户端发送功能列表，显示登录后哪些新功能已经解锁，哪些旧功能已经消失。这个列表中的一些标准扩展名是`UIDPLUS`、`MOVE`和`CONDSTORE`。一些 Gmail 支持但其他地方不支持的自定义扩展是`ESEARCH`和`X-GM-EXT-1`。

### 列出文件夹

好了，现在我们登录了，让我们试着做一些有用的事情。首先，让我们检查邮箱中存在哪些文件夹:

```
tag4 LIST
tag4 BAD Could not parse command 
```

Enter fullscreen mode Exit fullscreen mode

哎呦！正如你所看到的，如果你把一个命令的参数弄错了，服务器会拒绝它并说`BAD` client！

```
tag5 LIST "" "*"
* LIST (\HasNoChildren) "/" "Call log"
* LIST (\HasNoChildren) "/" "INBOX"
* LIST (\HasChildren \Noselect) "/" "[Gmail]"
* LIST (\All \HasNoChildren) "/" "[Gmail]/All Mail"
* LIST (\Drafts \HasNoChildren) "/" "[Gmail]/Drafts"
* LIST (\HasNoChildren \Important) "/" "[Gmail]/Important"
* LIST (\HasNoChildren \Sent) "/" "[Gmail]/Sent Mail"
* LIST (\HasNoChildren \Junk) "/" "[Gmail]/Spam"
* LIST (\Flagged \HasNoChildren) "/" "[Gmail]/Starred"
* LIST (\HasNoChildren \Trash) "/" "[Gmail]/Trash"
* LIST (\HasNoChildren) "/" "alabel"
tag5 OK Success 
```

Enter fullscreen mode Exit fullscreen mode

### 文件夹标志

这里我们可以看到邮箱中存在的文件夹层次结构。文件夹有特殊标志，用括号表示。其中一些标志对于遍历文件夹层次结构很有用，如`\HasNoChildren`和`\HasChilden`，而其他标志则用于表示给定文件夹的特殊特征，如它是否包含草稿(`\Drafts`)或已发送邮件(`\Sent`)。这些标志允许客户端根据邮箱定制自己的行为，并做一些事情，如将发送的邮件保存到正确的文件夹中，而不管用户的邮箱使用哪种语言。

### 会话

好的，现在让我们读一些电子邮件。首先，因为 IMAP 连接是有状态的，我们需要使用`SELECT`命令在我们关心的文件夹上打开一个会话:

```
tag6 SELECT "INBOX"
* FLAGS (\Answered \Flagged \Draft \Deleted \Seen $NotPhishing $Phishing)
* OK [PERMANENTFLAGS (\Answered \Flagged \Draft \Deleted \Seen $NotPhishing $Phishing \*)] Flags permitted.
* OK [UIDVALIDITY 1] UIDs valid.
* 55 EXISTS
* 0 RECENT
* OK [UIDNEXT 54948] Predicted next UID.
* OK [HIGHESTMODSEQ 11865059]
tag6 OK [READ-WRITE] INBOX selected. (Success) 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多服务器发送给我们的信息！会话是 IMAP 工作方式的一个关键组成部分，会话从你`SELECT`一个邮箱开始，一直持续到你`UNSELECT`它、`SELECT`另一个邮箱，或者注销。

打开会话时，除了表示成功的 OK 之外，该服务器还向我们发送了:

*   对邮箱中的邮件使用的活动标志列表
*   可用于邮箱中邮件的受支持标志列表
*   `UIDVALIDITY` -稍后再详细介绍
*   邮箱中的邮件数量
*   邮箱中有多少“最近”的邮件，这意味着有多少邮件标有`\Recent`标志
*   “预测的下一个 UID”
*   一个`HIGHESTMODSEQ`值——稍后会详细介绍

其中一些项目需要更多的解释。第一，UIDs。

### UIDs &序号

提醒一下:很久很久以前，有了 IMAP2，在 IMAP2 中，消息是通过序列号来识别的。序列号实际上就是单调递增的数字，邮箱中的每封邮件都有一个序列号:

```
tag7 SEARCH ALL
* SEARCH 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55
tag7 OK SEARCH completed (Success) 
```

Enter fullscreen mode Exit fullscreen mode

每次你选择一个邮箱，如果有新的信息，就会有更多的序列号。如果您移动了消息并开始了新的会话，分配给消息的序列号可能会与旧会话中的不同。

如果你所做的只是连接，在线阅读你的邮件，然后断开连接，这个非常简单的方案非常有效。但是，如果您想要连接，将所有邮件下载到本地以供脱机访问，然后稍后重新连接，并让您的客户端将其本地状态与服务器上的状态同步，这是行不通的，因为这些非持久序列号不允许客户端将本地邮箱状态映射到远程邮箱状态，因为每端的邮件可能有不同的 id！

输入 IMAP4 和 uid。只要消息位于同一个文件夹中，并且服务器的 UIDVALIDITY 整数没有增加，uid(唯一 id)对于该消息就是持久的。因此，当客户端在邮箱上启动新会话时，它必须检查 UIDVALIDITY 并将其与本地缓存值进行比较，如果服务器的 UIDVALIDITY 较高，它必须丢弃所有缓存的本地 uid 并从头开始重新同步。但除此之外，它可以自由地将其本地缓存数据与服务器上的数据进行比较，并且只同步更改。

为了保持向后兼容性，IMAP4 支持序列号和 uid，您可以通过在任何返回消息标识符的命令前面加上`UID`来请求 uid:

```
tag8 UID SEARCH ALL
* SEARCH 53809 54180 54185 54211 54222 54268 54410 54451 54452 54453 54463 54527 54557 54595 54596 54602 54608 54617 54637 54639 54643 54664 54667 54670 54673 54699 54707 54738 54753 54754 54804 54806 54847 54852 54857 54864 54873 54877 54880 54881 54883 54888 54895 54899 54902 54903 54904 54905 54906 54916 54923 54926 54931 54942 54947
tag8 OK SEARCH completed (Success) 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，与序列号不同，uid 不能保证以 1 为增量递增—当邮件到达并在文件夹之间移动时，分配给邮箱的 uid 会出现缺口。

### 有状态

当我们在这里的时候，有一个关于状态的题外话:一般来说，状态在协议中不是一个理想的特性。有状态性使得在 IMAP 中使用连接池变得更加困难，因为`SELECT`命令可能非常慢，并且每次池消费者检查新连接时，它都需要确保选择了正确的邮箱。有状态性还意味着应用程序代码必须跟踪状态，并确保它与连接的实际状态保持同步，否则每次需要信息时都要让另一个客户机-服务器来回切换。通常，使用无状态协议比使用有状态协议更容易编程。

### 获取报文数据

好了，让我们最后真正从消息中下载数据吧！

```
tag13 UID FETCH 53809 (FLAGS BODY[HEADER.FIELDS (DATE FROM)])
* 1 FETCH (UID 53809 FLAGS (\Answered \Seen) BODY[HEADER.FIELDS (DATE FROM)] {80}
From: My Friend <friends4ever@gmail.com>
Date: Sun, 18 Feb 2018 10:50:34 -0500

)
* 55 EXPUNGE
* 54 EXISTS
tag13 OK Success 
```

Enter fullscreen mode Exit fullscreen mode

IMAP 提供了许多旋钮，允许客户端请求有关邮件的部分数据，包括邮件的大小、标志(如表示邮件已被阅读的`\Seen`和表示邮件已被回复的`\Answered`)、单个标题和 MIME 邮件中的单个 MIME 部分，这允许客户端通过仅下载他们需要的数据来优化他们的带宽使用。例如，客户端可以快速下载文件夹中所有邮件的基本标题，以便生成邮件列表供用户浏览，然后按需获取邮件正文。

在结束之前，我们还想介绍一些关于 IMAP 的概念。

### 主动回复&无所事事

在前面的例子中，您可能已经注意到一些奇怪的未标记的响应:

```
* 55 EXPUNGE
* 54 EXISTS 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是关于一封邮件从邮箱中消失，而另一封邮件在我们打开邮箱时出现的通知。在 IMAP 中，服务器可以随时向连接的客户端发送这些未经请求的消息。还有一种模式可以让 IMAP 连接进入，称为 [IDLE](https://tools.ietf.org/html/rfc2177) ，它本质上只用于处理关于邮箱更改的通知，通常被客户端用来快速同步新邮件。

### 断开同步

离线同步允许电子邮件应用程序在飞机 wifi 和糟糕的蜂窝网络连接面前工作良好。与其说它是 IMAP 的一个一流特性，不如说它是一个可以在协议之上花费大量精力构建的东西，因为 IMAP 最初并不是为支持它而设计的。

使用 IMAP4 以其原始形式进行离线同步的方法非常简单:您必须获取本地每个文件夹的 uid 列表，并将其与服务器上当前文件夹中的 uid 列表进行比较，然后根据差异获取新邮件并删除旧邮件。随着邮箱的增长，客户端处理起来变得越来越复杂，后来的扩展如 [CONDSTORE 和 QRESYNC](https://tools.ietf.org/html/rfc7162) 改进了断开连接的重新同步流程。但是，即使有了这些扩展，进行断开连接的重新同步的过程实现起来也很复杂——即使在今天，许多 IMAP 服务器实现也不支持较新的扩展，因此通用客户机必须包含用于不同服务器的条件逻辑。关于如何在 IMAP 上实现离线同步的一般参考是 [RFC4549](https://tools.ietf.org/html/rfc4549) 。

## 总结

好吧，我们做到了！通过阅读本文，您已经了解了 IMAP 的历史和主要组件，IMAP 是一种用于访问和操作远程邮箱的开放协议。我们已经连接到 IMAP 服务器，登录到我们的电子邮件帐户，列出了远程服务器上的文件夹，并下载了邮件数据。这正是当你浏览邮箱时，在电子邮件应用程序中发生的事情。我们还了解到，由于 IMAP 多年来的发展，客户端实现离线同步相当复杂——这就是为什么我们构建了我们的 [delta API](https://docs.nylas.com/reference#deltas) 来简化这个过程。

下一次，我们将深入探讨电子邮件的另一个重要的开放协议:SMTP，这是电子邮件发送的方式。我们希望你能留下来，和我们一起分享快乐！

* * *

This post originally appeared on the [Nylas Engineering blog](https://www.nylas.com/blog/nylas-imap-therefore-i-am).