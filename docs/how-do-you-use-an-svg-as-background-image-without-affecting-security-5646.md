# 如何在不影响安全性的情况下使用 SVG 作为背景图像？

> 原文：<https://dev.to/drishit96/how-do-you-use-an-svg-as-background-image-without-affecting-security-5646>

我想使用一个 SVG 作为我的一个页面的背景图像。为此，我必须将我的 CSP img-src 规则设为`img-src 'self' data:`
，然而，这将是一个不安全的选择，如这里的[所解释的](https://security.stackexchange.com/a/95011)。

有人面对过这个问题吗？如果是，你是怎么克服的？