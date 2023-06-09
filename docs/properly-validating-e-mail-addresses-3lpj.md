# 正确验证电子邮件地址

> 原文：<https://dev.to/tux0r/properly-validating-e-mail-addresses-3lpj>

如果您是“web 应用程序”的开发人员，您可能已经编写(或复制粘贴)了一些试图验证输入的电子邮件地址的代码。如果您确实这样做了，并且您的解决方案包含一个长而难读的正则表达式(就像在堆栈溢出中看到的大多数表达式一样),那么我有一个坏消息要告诉您:您的代码是错误的。

您是否知道括号、空格和——根据 [RFC 6531](https://tools.ietf.org/html/rfc6531) 文档——表情符号可以是有效电子邮件地址的一部分——这仅仅是一个*实现细节*是否应该支持 Unicode*？*

 *您是否知道 IPv6 地址和您的 intranet 中的资源都是“@”后面部分的有效部分，因此要求 TLD ( *xxxx.yy* )是完全错误的？

也不是所有现存的(而且比“有没有一个`@`字符？”)我遇到过的验证器。RFC 比任何正则表达式都要灵活得多，包括特殊情况，比如某些字符只允许出现在带引号的字符串中，但是带引号的字符串不能出现在两个句点之间。啊，*标准*的喜悦。

当然，您仍然可以使用足够复杂的正则表达式来捕获大多数无效的电子邮件地址，但是我强烈建议您不要只追求“几乎没有错误”的代码。所以这里有几个建议:

1.  向您想要验证的所有电子邮件地址发送验证邮件。

    如果用户确认已经收到，那么该地址是完全有效的。但是这并不真正可靠，因为没有人被迫点击进入电子邮件，如果用户不这样做，这并不自动意味着地址无效。这个建议*只有*对于像网络论坛这样的事情是有意义的，在那里确认电子邮件可以被正确地宣布*并且*点击进入它可能是必需的。

2.  阅读 RFC。

    你可能不想这么做。:-)不过别担心，我是为你做的:

3.  **使用`libvldmail`。**

    [`libvldmail`](https://github.com/dertuxmalwieder/libvldmail) 是一个用 C 语言编写的小型、简洁的库(没有特别的依赖，甚至连`regex.h`也没有)，我开发它是为了一劳永逸地解决这个问题。我也提供语言绑定(通过 [SWIG](http://swig.org/) )，所以如果你的 web 应用运行在 Python 或 Ruby 或其他什么之上，你应该能够安全地使用它。SWIG 网站上有一个[教程](http://swig.org/Doc3.0/SWIG.html#SWIG_nn2)，教你如何从提供的模板文件生成绑定，如果你需要的话。

请阅读自述文件和[示例实现](https://github.com/dertuxmalwieder/libvldmail/blob/master/test.c)，了解如何使用它的详细信息。别担心，我把它简化了:只有一个函数[和一个参数](https://github.com/dertuxmalwieder/libvldmail/blob/master/src/vldmail.h)。

我只是觉得这对你们有些人会有用。*