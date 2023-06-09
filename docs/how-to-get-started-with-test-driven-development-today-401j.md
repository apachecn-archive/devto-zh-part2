# 今天如何开始测试驱动开发

> 原文：<https://dev.to/kylegalbraith/how-to-get-started-with-test-driven-development-today-401j>

测试驱动开发(TDD)是在编写任何代码之前*编写测试的行为。也称为红/绿测试，因为您编写了测试，它失败了，然后您编写代码使它通过。*

这个过程有很多不同的好处，比如更简单的设计，更多的测试覆盖，和改进的代码质量。它为开发人员提供了一个结构，通常可以产生有用的编码标准。如果一个团队采用 TDD，那么**所有的**开发人员为他们编写的代码编写测试。

但是如果你对 TDD 完全陌生，那么开始使用它可能是模糊的。原来入门还挺简单的。

## 把脚趾伸进 TDD

在理想世界中，所有的特性都有其预期的行为。但是在现实中，产品经理会改变他们的想法，用例会改变，预期的行为也会随之改变。这给 TDD 增加了一个转折，一开始可能会令人沮丧。

对于 TDD 新手来说，试图在一个特性开发出来之前编写测试可能是一个精神障碍。你感到被 TDD 卡住或放慢了速度，因为预期的行为还没有确定下来。随着需求的变化，您经常需要迭代您的测试。这不是一件坏事，但这可能是一个具有挑战性的起点。

事实上，正是这种扭曲导致涉众说“TDD 耗时太长”。事实上，TDD 是为了将来更快的发展做准备。编写的每一个测试都是更好的代码质量和更快的开发过程中的一个缺口。

如果您正在使用遗留代码库，那么 TDD 实验就差一个 bug 了。每一个软件都有它们，它们往往有预期的和实际的行为。这些都是开始应用 TDD 概念的好地方。

遵循这 7 个步骤来熟悉测试驱动开发。

1.  bug 必须是可重现的，并且具有预期的行为。
2.  现在找到代码中的 bug 所在的位置。
3.  创建具有预期行为的单元测试。
4.  运行您的新测试，看看它是否失败。
5.  更新代码以产生预期的行为。
6.  再次运行您的新测试，看看它是否通过。
7.  对测试覆盖的代码执行任何重构。

就这样，您应用了 TDD 概念。简单吧？几乎太简单了。关于其中的几个步骤，我忽略了一些重要的细节。

### bug 必须是可重现的，并且有预期的行为

如果有一个关于是否是一个 bug 的公开问题，那么它就不是一个开始你的 TDD 冒险的好 bug。为什么？因为这些类型的错误具有不明确的预期行为，所以您的测试应该测试什么？

### 创建一个具有预期行为的单元测试

在已经有单元测试的遗留系统中，这可能是微不足道的。如果你在一个还没有任何单元测试的系统中，那么你正在开拓新的领域。随着新领域的出现，重构代码应运而生。为什么？因为这些代码库通常需要是可测试的。添加依赖注入，使类和函数成为单一责任，也许还可以使用接口。

### 对测试覆盖的代码执行任何重构

这是露营地的政策，留下比你发现的更好的代码。如果你认为它是没有污点的，请与你团队中的某个人再次确认。代码是一个活的有机体，随着每一次提交而进化。它必须得到维护、清洁和优化。有了好的单元测试，您就可以进行这种维护，并且知道您是否破坏了您不应该破坏的东西。

## 特性呢？

一旦你熟悉了 TDD 概念，你也应该将它们应用到新的特性中。该过程在很大程度上是相同的。有些人写了几行特性，然后写了测试。其他人编写所有的测试，然后编写特性。

对于哪一个是正确的，有一系列的 TDD 人员有非常强烈的意见。我认为它们都很棒，因为无论哪种方式都有考验。

变化的需求呢？没什么大不了的。为什么？因为如果需求改变了，那么你就更新你的测试，看到它失败，然后更新代码。通过在特性之前/与特性并行地编写测试，迭代特性是轻而易举的事情。

## 结论

测试驱动开发(TDD)是创建软件的一个非常强大的工具。它使团队能够开发可维护的高质量代码。但是，它不是唯一的工具。结对编程、小故事和快速迭代开发也是需要的关键工具。

### 通过实际使用来学习 AWS

如果你喜欢这篇文章，并且渴望开始学习更多关于 Amazon Web Services 的知识，我已经创建了一个关于如何在 AWS 上托管、保护和交付静态网站的新课程！这是一个书籍和视频课程，从信息的海洋中切入，加速你对 AWS 的学习。给你一个框架，让你通过实际使用来学习复杂的东西。

前往登录页面，为自己获取一份副本！