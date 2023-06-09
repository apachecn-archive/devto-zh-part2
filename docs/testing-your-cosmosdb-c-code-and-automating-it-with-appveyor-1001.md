# 使用 AppVeyor 测试您的 CosmosDB C#代码并使其自动化

> 原文：<https://dev.to/elfocrash/testing-your-cosmosdb-c-code-and-automating-it-with-appveyor-1001>

#### 简介

我们不都喜欢测试良好的代码吗？我知道我有。知道如果我改变这边的东西，那边的东西不会坏掉，这种感觉太棒了。

然而，当你的代码依赖于第三方库时，你必须希望该库中的代码易于使用和测试。

如果这不是你第一次阅读我的博客，那么你可能知道我正在为 CosmosDB 开发一个名为 [Cosmonaut](https://github.com/Elfocrash/Cosmonaut) 的开源 ORM。当我使用第三方库，并且它的代码碰巧是开源的，那么我做的第一件事就是去托管它的代码的地方，检查是否有覆盖它的测试。没有人想使用未经测试的代码，所以当我开始的时候，我知道我应该覆盖尽可能多的场景和案例。

#### 单元测试

在单元测试方面，CosmosDB SDK 并不像它应该的那样友好(CosmosDB SDK 团队知道这一点，所以它会变得更好)。通常，对这样的代码进行单元测试的方法是模拟通过网络进行的调用，并返回您希望这个场景返回的数据集。

然而，这里列出了一些让你很难测试你的代码的事情。

##### 问题 1

`ResourceResponse`类的构造函数包含标记为`internal`的`DocumentServiceResponse`参数。这是不好的，因为即使你可以从你的 DTO 类中创建一个`ResourceReponse`对象，你也不能设置像 RUs consumed，response code 和几乎任何其他东西，因为它们都来自`ResourceResponseBase`,它也有标记为内部的`DocumentServiceResponse`。

##### 解

为了解决这个问题，您必须以某种方式为`ResourceResponse`设置响应头。你可能已经猜到了，那么你能做到这一点的唯一方法就是通过反射。这里有一个扩展方法，可以将你的泛型类型对象转换成一个`ResourceResponse`，并允许你设置`responseHeaders`。