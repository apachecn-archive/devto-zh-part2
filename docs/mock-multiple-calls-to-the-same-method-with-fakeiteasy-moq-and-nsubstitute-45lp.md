# 用 FakeItEasy、Moq 和 NSubstitute 模拟对同一个方法的多次调用

> 原文：<https://dev.to/matheusrodrigues/mock-multiple-calls-to-the-same-method-with-fakeiteasy-moq-and-nsubstitute-45lp>

你可以在 [GitHub](https://github.com/mrodrigues1/MockMultipleCalls) 上下载这篇文章项目。

有时候在做单元测试的时候，我们会遇到对同一个方法的后续调用。模仿一个方法时，默认行为是总是返回相同的结果。

在这篇文章中，我将展示如何使用模仿框架 [FakeItEasy](https://fakeiteasy.github.io/) 、 [Moq](https://github.com/moq/moq4) 和[nsubiste](http://nsubstitute.github.io/)来模仿对同一个方法的多次调用。

[继续阅读...](https://www.matheus.ro/2018/03/26/mock-multiple-calls-to-the-same-method-with-fakeiteasy-moq-and-nsubstitute/)