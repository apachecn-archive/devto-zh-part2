# 处理密码

> 原文：<https://dev.to/devmazee2057282/handling-passwords-gfk>

## 规则

*   最小长度为 8 或 16 个字符。
*   最大长度为 2，048 个字符。
*   检查密码是否违规？(可选)

这可能是一个好主意，以确保他们没有把一个共同的或违反密码。我没有其他的规则，很简单。我认为拥有一个或多个数字，符号，大写字母，有点被高估了。我并不真的需要它，不是吗？关于为什么一个应用程序需要 T1 或者 T2 应该有 T3 这样的东西，有什么例子或者场景吗？请分享。

我鼓励写密码的人。

## 储存

至于存储密码，我不会让它成为纯文本，当然，除非我的目的是窃取凭证。所以我会使用一个强大的哈希算法，比如 Bcrypt 或 Argon2 的一些变体。散列密码已经足够好了，至少我们中的一些人是这样认为的。虽然我不怀疑散列密码是好的，但我相信它可以进一步保护密码。

是的，*加密*散列，用一些强的东西。最好是 AES-256 或 AES-128。也许还有一种更复杂的方法，用 RSA 签名密钥，然后在此基础上用 AES 加密。不打算太深入(我不想开始谈论我不理解的东西)。

这样做让黑客/破解者无论你想怎么称呼他们，又进了一步。解密散列，然后最终处理散列。

## 总结

确保密码在(8 或 16)-2，048 个字符的阈值范围内，可以选择检查密码是否常见或被破解。散列密码，然后加密散列密码，最后存储在某个地方。

## 推荐

当你阅读密码的时候，看看这些。

[![paragoniescott image](../Images/67b58e44f82682b23d2c381b2ff2f337.png)](/paragoniescott) [## 你不会用 64 为基数的密码！面向开发人员的加密术语和概念

### Scott Arciszewski 12 月 13 日 1614 分钟阅读](/paragonie/you-wouldnt-base64-a-password-cryptography-terms-and-concepts-for-developers) [![domysee image](../Images/8470100259a1c66f0c9c832a5ff8caa0.png)](/domysee) [## 密码规则会影响安全性吗？

### 张秀坤·韦伯 7 月 8 日 181 分钟阅读

#security #passwords #rules](/domysee/do-password-rules-impact-security-137n)[![rdegges image](../Images/751d3a70466613d2e14ba00f2738fd4e.png)](/rdegges) [## 如何防止您的用户使用被破解的密码

### Randall de gges 6 月 11 日 184 分钟阅读

#programming #security #javascript](/oktadev/how-to-prevent-your-users-from-using-breached-passwords-5cne)