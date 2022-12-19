# 在后 PGP 时代加密文件

> 原文：<https://dev.to/artis3n/encrypting-files-in-a-post-pgp-age-59bf>

> ![Thomas H. Ptacek profile image](../Images/2f0b6f712e28d49cd3dab0a20994c7a8.png)托马斯·h·普塔切克[@ tqbf](https://dev.to/tqbf)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我曾目睹一位颇有名气的密码学家用 spitball 攻击 PGP MDC，然后说“去他妈的，我不打算花时间研究 PGP”。这大概是我对 PGP 在现代加密领域的看法。2018 年 5 月 18 日 21 点 41 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=997593091094794241)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=997593091094794241)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=997593091094794241)

由于最近 [EFail 的披露](https://efail.de/)，一场关于 PGP 的安全性(它缺乏)和可用性(它很复杂)的新对话已经开始。这是一次很好的谈话，虽然我有自己的观点，但我欢迎大家在下面的评论中发表意见。我知道“后 PGP 时代”这个术语会引起 InfoSec 社区的一些成员的关注，他们会认为强大、安全的 PGP 客户端仍然是任何用例的理想解决方案，或者至少是大多数用例的理想解决方案。我写这篇文章的目的并不是要论证 PGP 应该消亡。相反，我想概述 PGP 的合理替代方案，读者可能不知道，所以他们可以对他们的用例做出明智的决定。

## PGP 有什么问题？

密码学教授 Matthew Green 有一篇很棒的文章详细介绍了 2014 年 PGP 的技术问题，Keybase 列出了一系列 PGP 的[密码问题。我们将总结两个最麻烦的问题，但这些文章绝对值得一读。](https://saltpack.org/pgp-message-format-problems)

PGP 最大的问题是，按照现代标准，加密技术是不够的。首先，PGP 不支持[前向保密](https://en.wikipedia.org/wiki/Forward_secrecy)。前向保密性是密码系统的一个特性，即使过去使用的密钥在将来被泄露，它也能保持保密性。每个会话都有一个不同的密钥。在某些情况下，例如使用[信号](https://www.signal.org/)，每条消息都有一个新的会话密钥，因此，如果密钥被泄露，它只会影响特定的消息。对话的其余部分仍然保密。如果 PGP 中没有前向保密，如果攻击者控制了任何一方的私钥，整个会话的历史都会被破坏。

> ![Steve Weis profile image](../Images/f346bc3fffe11e8469215cf994ea11f9.png)史蒂夫·维斯@斯威思![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ Hacker _ Horse](https://twitter.com/Hacker_Horse)[@ Matthew _ d _ green](https://twitter.com/matthew_d_green)我帮助脸书建立了 PGP 支持，在这一点上，它可能是世界上发送 PGP 邮件最多的人。
> 
> 想要权威，菲尔·齐默曼已经停止使用 PGP。
> 
> 写的哥们儿不用。2018 年 5 月 17 日 21 点 43 分[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=997231087108550658)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=997231087108550658)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=997231087108550658)

此外，PGP 默认不使用[认证加密](https://crypto.stackexchange.com/questions/12178/why-should-i-use-authenticated-encryption-instead-of-just-encryption)。认证加密是一种将密文的机密性和完整性结合到一个操作中的属性。自 2001 年以来，OpenPGP 就支持修改检测代码(MDC)选项[，但它必须由客户端实现来选择，而许多客户端实现都没有。安全的密码系统应该实施安全的默认值。当然，维护对遗留系统的支持是有道理的，但是在 15 年以后，开始要求这个选项应该是可以接受的。](https://lists.gnupg.org/pipermail/gnupg-users/2018-May/060315.html)

## 我有哪些替代品？

> ![Matthew Green profile image](../Images/95346cd0be3fc07f2b476ca9cf50ecf9.png)马修·格林@马修 _ d _ 格林![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)这就是 PGP 的问题:每当有人有一个远程涉及加密文件的用例时，一些人就认为这意味着我们必须把所有五百页的 OpenPGP 和 GnuPG 都拖进来。不:不是。2018 年 5 月 18 日上午 10:41[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=997426990805409793)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=997426990805409793)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=997426990805409793)

如果您需要发送加密电子邮件或保护敏感文件，如果不使用 PGP，您会使用什么？让我们看看外面还有什么。

### [OpenSSL](#openssl)

首先，我想说，虽然 OpenSSL 是一个可用的替代方案，但是您不应该选择它。使用 OpenSSL 进行公钥加密是一个漫长的过程，而且有更简单的选项可以提供更强的安全性。但是，让我们看看它是如何工作的。用适当的值替换任何`%wrapped%`文本。

1)获取`pem`格式的接收方公钥，并转到步骤 2。如果密钥是`rsa`格式，比如为 SSH 生成的密钥，接收方必须执行以下操作来转换他们的公钥和私钥:

```
openssl rsa -in %RSA PRIVKEY% -outform pem > %PRIVKEY%.pem
openssl rsa -in %RSA PRIVKEY% -pubout -outform pem > %PUBKEY%.pem 
```

Enter fullscreen mode Exit fullscreen mode

2)作为发送方，生成一个随机密钥:

```
openssl rand -base64 32 > %KEY%.bin 
```

Enter fullscreen mode Exit fullscreen mode

`-base64`标志中的 32 个字节产生一个 256 位的密钥。

3)发送方加密密钥:

```
openssl rsautl -encrypt -inkey %PUBKEY%.pem -pubin -in %KEY%.bin -out %KEY%.bin.enc 
```

Enter fullscreen mode Exit fullscreen mode

4)发送方加密文件:

```
openssl enc -aes-256-cbc -salt -in %FILE_TO_ENCRYPT% -out %ENCRYPTED_FILE%.enc -pass file:%ABSOLUTE_PATH_TO_KEY%.bin 
```

Enter fullscreen mode Exit fullscreen mode

1)发送方将文件和加密密钥传输给接收方。接收者现在解密密钥:

```
openssl rsautl -decrypt -inkey %PRIVKEY%.pem -in %KEY%.bin.enc -out %KEY%.bin 
```

Enter fullscreen mode Exit fullscreen mode

6)并且接收者现在解密文件:

```
openssl enc -d -aes-256-cbc -in %ENCRYPTED_FILE%.enc -out %FILE% -pass file:%ABSOLUTE_PATH_TO_KEY%.bin 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们只剩下 5-6 步。除非您使用 [LibreSSL](https://www.libressl.org/) ，否则 AES-GCM 模式将不可用，这意味着您必须在加密时选择`aes-256-cbc`选项。AES-GCM 是 AES 的认证加密模式，但是在本文发表时，OpenSSL 中还没有认证加密。如果您必须使用 OpenSSL，我推荐您使用 LibreSSL，使用它您将拥有认证的加密模式。

对于 OpenSSL，我们有一个繁琐的过程，甚至不能解决(除非您使用 libres sl)PGP 的两个主要加密问题之一。让我们看看我们还能用什么。

### 盐包

[Saltpack](https://saltpack.org/) 是由 [Keybase](https://keybase.io/) 开发的一种新的加密格式，它建立在 [NaCL](https://nacl.cr.yp.to/) 加密库之上。Saltpack 是专门为改进 PGP 的安全缺陷而设计的。关于加密规范的细节可以在[这里](https://saltpack.org/encryption-format-v2)找到。它提供认证加密和前向保密，以及其他保证，如可否认的认证。你如何使用它？有两个选择。

#### 使用 Keybase 客户端

发送方和接收方必须[安装密钥库](https://keybase.io/download)。

1)然后，发送者加密信息:

```
keybase encrypt %RECIPIENT_KEYBASE_USERNAME% -m "%MESSAGE%"
# Or, read the message in from a file
keybase encrypt %RECIPIENT_KEYBASE_USERNAME < %MESSAGEFILE% 
```

Enter fullscreen mode Exit fullscreen mode

2)接收方解密消息:

```
keybase decrypt -m "%MESSAGE%"
# Or, from a file
keybase decrypt < %MESSAGEFILE% 
```

Enter fullscreen mode Exit fullscreen mode

还有`sign`和`verify`命令可用。

#### 为你的编程语言使用 Saltpack 包

目前有 [Go](https://godoc.org/github.com/keybase/saltpack) 和 [Python](https://github.com/keybase/saltpack-python) 中的 Saltpack 包。但是，Go 包是 Keybase 使用的包，功能齐全。Python 包在特性支持方面比较落后，至少根据 Github 上 Python 包的自述是这样的。您仍然可以使用 Python 包执行与 Go 包相同的加密、解密、签名和验证。

要安装:

```
# Go
go get github.com/keybase/saltpack
# Python - requires Python 3
pip install saltpack 
```

Enter fullscreen mode Exit fullscreen mode

Godocs 包含 Go 库的示例用法，所以让我们看看 Python 的用法是什么样的:

1)发送方加密信息:

```
python3 -m saltpack encrypt "%RECIPIENT_PUBKEY%" -m "%MESSAGE%" > %ENCRYPTED%.enc 
```

Enter fullscreen mode Exit fullscreen mode

1)接收者解密消息:

```
python3 -m saltpack decrypt "%RECIPIENT_PRIVKEY%" < %ENCRYPTED%.enc 
```

Enter fullscreen mode Exit fullscreen mode

Saltpack，尤其是通过 Keybase 客户端，对用户来说是一个很好的选择。服务器可以通过 CLI 运行这些命令，而用户也可以选择 Keybase 的应用程序或 Keybase 网站。

### 其他

最后，还有其他替代方案，如 [libpqcrypto](https://libpqcrypto.org/index.html) ，它们需要开发投资，但也可以作为 PGP 的替代品。还有许多 NaCl 客户端可以自举来支持类似 PGP 的行为，同时利用更强的加密。对于“即插即用”的加密电子邮件和文件支持，我推荐看看 Saltpack。

对文章的看法？你有其他选择吗？留言评论！