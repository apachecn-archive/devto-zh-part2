# 关于为 Java 代码开发一个新的静态分析器的几句话

> 原文：<https://dev.to/alexastva/couple-of-words-about-development-of-a-new-static-analyzer-for-java-code-3dmf>

[![How Dr. Frankenstein assembled the analyzer from pieces](../Images/cc18308b850d64f2c10426015c35c190.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1f7juRQM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.viva64.com/media/images/content/b/0572_Java_analyzer/image4.png)

PVS-Studio static analyzer 团队直到最近还在搜索 C、C++和 C#代码中的错误和潜在漏洞，他们也为 Java 代码准备了新版本的工具。尽管事实上在 Java 世界中已经有许多静态分析工具，开发者相信他们的分析器是强大的，并且将是一个很好的竞争对手。

一名开发人员在他的文章中写道 PVS-Studio for Java 是如何创建的。首先，有必要弄清楚语法树和语义模型的开发过程。由于这些元素是基本的，静态分析器是围绕它们构建的。除此之外，分析器还需要数据流分析，这使您能够计算程序中每一点的变量和表达式的可能值，并由此找到错误。分析器还需要注释机制、诊断规则、集成、测试和其他技术，本文将对此进行详细解释。

传统上，作者给出了 Java 分析器检测到的来自不同开源项目的错误的例子。作者还指出，在未来的文章中，每个项目将提供更详细的报告。到目前为止，您可以查看来自 Hibernate、Hive、JavaParser、Jenkins 项目和其他几个项目的错误。

除此之外，当 Java analyzer 的第一个 alpha 版本可用时，开发人员建议那些愿意的人参与它的测试。要做到这一点，写信给他们的支持，他们会与您联系。

全篇-[https://www.viva64.com/en/b/0572/](https://www.viva64.com/en/b/0572/)