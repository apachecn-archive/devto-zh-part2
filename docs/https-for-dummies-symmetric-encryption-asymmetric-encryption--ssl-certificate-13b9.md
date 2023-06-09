# 虚拟 HTTPS:对称加密、非对称加密和 SSL 证书

> 原文：<https://dev.to/apium_hub/https-for-dummies-symmetric-encryption-asymmetric-encryption--ssl-certificate-13b9>

在 HTTPS 傻瓜文章中，我们将看到什么是 HTTPS 以及为什么最后的 S 是如此重要。

先从基础说起。HTTP 是一种通信协议，而 HTTPS 是具有安全层的 HTTP，该安全层增加了保密性和更多的安全属性。

## HTTPS 对假人:非对称加密

不对称密码术基于基于两个密钥的信息编码，一个私钥和一个公钥。顾名思义，私有密钥必须由发送者保存，而公共密钥可以发送给任何接收者。使用私钥，我们可以用公钥解密所有加密的消息，而使用公钥，我们只能用私钥解密加密的消息。

**1。公钥加密**

正如我提到的，公钥可以加密消息，而这些消息稍后只能用私钥解密。这意味着没有人能用公钥解密该消息。

像这样我们得到保密。发送方用接收方的公钥加密了消息，所以除了接收方，没有人能够解释消息。

**2。用私钥加密(数字签名)**

用私钥，我们可以加密任何拥有公钥对私钥的人都能读取的信息。这个过程没有给我们保密性(因为任何有公钥的人都可以阅读消息)，但是我们实现了**真实性**和**完整性**。我们得到了真实性，因为只有拥有私钥的人才能加密信息，而只有拥有公钥的人才能解密。

当只有用私钥加密消息的人得到一个能够用公钥解密的包，并且如果没有私钥，没有人能够解密、修改和重新加密它时，完整性就实现了。

## HTTPS 对假人:对称加密

重要的是要知道还有对称加密的概念。它比非对称协议更基本，但在使用 HTTPS 协议的客户端-服务器通信中起着重要作用。

不同的是，对称加密是基于单个密钥，既做加密又做解密。没有实现真实性和保密性，但另一方面，它更便宜，并且涉及发送者和接收者之间的密钥共享。

## HTTPS 假人:SSL 证书

根据[维基百科](https://simple.wikipedia.org/wiki/Digital_certificates),(数字)证书是:“由认证服务实体生成的计算机文件，该实体将身份数据与自然人或公司相关联，从而确认他们在互联网上的数字身份。”到目前为止，我们已经有了个人或组织加密信息并表明这是他们的信息的机制。然而，我们不知道他们是谁，也不知道这些信息是来自谷歌、脸书还是随机草药。

这里出现了 CA (Certificate authority 或 Certificate authority)的概念，是一个负责验证身份的实体。

申请证书的流程很简单，组织必须生成一个 CSR(证书申请)，将其发送给 CA，一旦该信息有效，就会基于该数字签名的 CSR 生成一个证书，然后发送给您，对吗？但是什么是企业社会责任呢？

CSR 是包含申请人信息的文本块，如域名、组织名称、城市、电子邮件等。和公钥。该 CSR 必须经过数字签名并发送给 CA。CA 和 CSR 一起找到公钥，用它来解密消息和验证数据。当 CA 确认您就是您所说的那个人时，他们会向您发送相同的证书，但是由他们进行了数字签名。

此时，我们唯一需要了解的是，这些 CA 是我们的操作系统甚至应用程序所依赖的实体(它们拥有这些 CA 的公钥)，如果像 GoDaddy 或 Comodo 这样的公司向您颁发由它们签署的数字证书，所有计算机都会相信这些信息是有效的。

## 《傻瓜 HTTPS》:HTTPS 是如何运作的？

我们已经看到了 HTTPS 沟通的所有必要元素，现在我们必须将它们结合起来。我们要做的是在使用非对称加密(安全)的客户机/服务器之间交换一个使用对称加密(性能)的密钥。

[![https for dummies](img/f08b0a984ade470c9359b9c62ff93109.png)T2】](https://apiumhub.com/wp-content/uploads/2018/02/Screen-Shot-2018-02-22-at-08.31.00.png)

概括地说，这个过程很简单，第一次通信是服务器(发送方)将证书和公钥一起发送给客户端(接收方)。客户端生成将用于对称加密通信的“秘密”,用公钥对其进行加密，并将其发送给服务器，并且它确保只有客户端/服务器拥有该密钥。

一旦服务器用会话密钥解密消息，对称通信就可以安全地开始了，因为密钥在某一时刻只对一个会话有效。

我希望这篇文章对你有用！

祝各位好运！

## 如果你觉得这篇关于 HTTPS 假人的文章很有趣，你可能会喜欢…

[安卓模拟器中的查尔斯代理](https://apiumhub.com/tech-blog-barcelona/charles-proxy-android-emulator/)

[回调 hell Nodejs:用光纤解决](https://apiumhub.com/tech-blog-barcelona/callback-hell/)

[面向对象设计中的单一责任原则](https://apiumhub.com/tech-blog-barcelona/single-responsibility-principle/)

[将提高你工作效率的 Tmux 指南](https://apiumhub.com/tech-blog-barcelona/tmux-cheat-sheet-tips-tricks/)

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子 [HTTPS 为假人:对称加密，非对称加密& SSL 证书](https://apiumhub.com/tech-blog-barcelona/https-for-dummies-ssl-certificate/)最早出现在 [Apiumhub](https://apiumhub.com) 上。