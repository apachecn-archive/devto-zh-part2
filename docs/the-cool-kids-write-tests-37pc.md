# 酷孩子写测试

> 原文：<https://dev.to/pancy/the-cool-kids-write-tests-37pc>

[![why does code work](img/67fbda7a8ea73bb470c6a1a1677ffaf0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7_R_LANh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/736x/ca/47/94/ca4794cfada458717c7aa99093a1f425--computer-humor-computer-science.jpg)

一般来说，软件测试有一个不好的名声。许多开发者将它与 SQA/SDET 联系在一起。他们认为他们没必要写测试，因为那应该留给守门员。我甚至认识一位 SQA 工程师，他自贬身份，称自己不是真正的工程师，还有人认为申请 SDET 的职位更容易进入一家知名公司。这些导致了一种错误的信念，即真正的工程师只会制造东西，而测试工程师是劣等的。

现实是完全不同的。在开源世界中，经过测试的代码表明质量和严肃性。测试可以作为对代码作者和其他相关人员的启发。它们可能是项目的最佳文档，也是对特定代码的意图或设计的极好概述。此外，在你写任何代码之前写测试(TDD)迫使你努力思考写干净，[点](https://blog.codinghorror.com/curlys-law-do-one-thing/)函数。与没有单一测试支持的一次性代码相比，它也给了作者更多的拥有感。

以前有人问我为什么喜欢写测试(有时假装困惑)。我这样做是因为编写测试是一种黑客行为。找到问题的最优解并有效地记录下来，这是一种系统性的黑客行为。我们一直都是这样做的！我们编写代码，运行它，回去修复一些错误，再次运行它，然后继续编写更多的代码。问题是，我们这样做了无数次，却没有构建我们的方法或使之自动化。例如，我们可能已经修复了一个 bug，并在付出巨大努力后成功地运行了我们的代码，但是几个月后我们可能会再次陷入同样的情况，我们将不得不像以前一样进行暴力调试，因为我们没有编写任何测试用例来记录第一次的发现。编写测试是一种解放。这才是真正的酷。

这并不是说我们必须为代码中的每个功能编写测试，或者必须痴迷于测试覆盖率。但是你当然不应该因为你没有时间或者你想[快速移动并且打破常规](https://mashable.com/2014/04/30/facebooks-new-mantra-move-fast-with-stability/#6UkgK3l0rPqj)而停止编写测试。因为当事情出错时，它们通常会花费更多的时间和精力，让你感觉很糟糕，更不喜欢编码。