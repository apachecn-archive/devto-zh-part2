# 我在三周内学到的密码学知识

> 原文：<https://dev.to/quantumsheep/what-i-learned-about-cryptography-in-3-weeks-57ok>

我开始开发文件加密命令行界面 [WarShield](https://dev.to/quantumsheep/protect-your-data-with-warshield-3n6i) 已经 3 周了。所以我想告诉你我今天所知道的所有关于密码学的重要事情。

* * *

# 密码学中的密钥(密码)

如果你想加密你的数据，最好也能解密它:这就是为什么你使用一个密钥，通常称为密码。

密码术中的密钥分为两类:

*   对称密钥密码术
*   公钥加密，也称为非对称密钥加密

对称密钥加密算法只使用一个密钥来加密和解密一个值，这与公钥加密算法不同，公钥加密算法使用“公钥”来加密，使用“私钥”来解密。

[![Encryption types (from https://ssl2buy.com)](../Images/cd6c5bfb1195ded151edd739353d5e04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzlxO6M6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kw4lrsuusznwbb9rxy0.png) 
<sup><sup>图片来源: [SSL2Buy](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)</sup></sup>

这些密钥必须与加密算法要求的长度完全相同。例如，像 AES-256-GCM 这样的算法需要一个 128 位的密钥(32 个字符)，GCM 是定义算法如何工作以提供像真实性这样的附加信息的“操作模式”。

但是不要担心，你不需要有 32 个字符的密码，尽管它是安全的。您可以(也必须)散列您的密钥来提供一个安全且大小合适的密钥。

* * *

# 随机性和真实性

如果你用同一个密钥加密多个值，输出会泄露一些关于你的密钥的信息。出于安全原因，您不希望出现这种情况。保护加密值的一个好方法是使它们完全随机。这是初始化向量。

## 初始化向量(IV)

这种技术基本上是一种将您的键与另一个值混合的方法，最好是一个随机值。

它的大小取决于定义的加密算法操作模式，并且必须手动给定。它不是由算法生成的。

## 认证和完整性

也许你听说过 MD5 算法来检查你下载的文件是否包含所有想要的数据？嗯，这叫正直。它确保您的数据正是您想要的。

身份验证涉及完整性，并确保您数据已由特定实体(例如，计算机)加密。这是一种确保数据不可否认性的方法，确切地说，就是确保数据不被篡改。认证值称为标签或 MAC(消息认证码)。

最常用的完整性算法是 SHA1 和 MD5。对于认证算法，它是 HMAC，校验和和 CMAC。还有很多其他算法，但这里是主要的算法。

[![MAC](../Images/8041ff51ed3b2f9fea22f8278b5953d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tcQP_Yuj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bopsnnslp48slw7ncujz.png) 
<sup><sup>图片来源:[维基](https://en.wikipedia.org/wiki/Message_authentication_code)</sup></sup>

* * *

和...就是这样！密码学的基础就没什么好说的了。当然，我可以谈论不同的加密算法，但我发现在这种情况下没有用。

我将学习越来越多的密码学知识，改进我的项目，也许还有我自己的安全性。希望你也学习了密码学😄