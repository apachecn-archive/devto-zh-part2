# 首先看看科特林——我的第一次痛苦

> 原文：<https://dev.to/joaofbantunes/first-look-at-kotlin---my-first-pains-5916>

## [T1】简介](#intro)

我一直想看看 Kotlin，但一直在拖延，但上周末我花了几个小时，我不知道如何使用它们，所以…我终于写了一些 Kotlin 代码(虽然结果大约有 30 行长，其中大部分是我在教程上看到的，但我还是键入了它，并看到它在我的计算机上运行！).

由于我并没有真正做什么有趣的事情，这篇文章基本上是关于我在跟随一些教程时遇到的一些问题。

## 入门

我已经安装了 IntelliJ(可能已经安装了很长时间),希望能和 Kotlin 一起玩，所以这一步是不可能的。我需要安装的是 Java JDK。

所需的软件安装完成后，我前往 Kotlin 网站，看看如何开始做一些事情。我找到了文档参考[页](http://kotlinlang.org/docs/reference/)，开始通读。在完成基础知识之后，我失去了耐心，并且在文档中注意到了用于构建 web 应用程序的 [Ktor](https://github.com/ktorio/ktor) ，于是我决定开始构建 web api。

然后按照 IntelliJ 和 Gradle 的 Ktor 文档上的说明，我很快就做好了一个简单的 web api，但是还有一些问题。

## (有问题的)代码

所以我最初的代码是这样的——不要用这个，它有问题！(而且都在`build.gradle`档上)。