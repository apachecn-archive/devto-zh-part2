# SBTCVM 的 SSTNPL 第 2 部分:摊牌

> 原文：<https://dev.to/thomasthespacefox/sbtcvms-sstnpl-part-2-cards-on-the-table-f83>

欢迎回到我的 SSTNPL 系列教程的第 2 部分，SSTNPL 是一种用于 9-trit 平衡三元虚拟机的编程语言，称为 SBTCVM。在[第 1 部分](https://dev.to/thomasthespacefox/sbtcvms-sstnpl-part-1-basics-3a7h)中，我们讨论了基本菜单并做了一些基本的变量设置。

在这一部分中，我们将看看如何使用 SSTNPL 的表，以及它们是如何非常有用的。

# 入门

像前面一样，将 tutorial_example_2.stnp 文件复制到安装 SBTCVM 的 VMUSER 目录中。

现在，你可能会问，SSTNPL 中的表是什么？简单地说，一个表格是一个“细胞”的 2D 阵列，可以通过 x 和 y 坐标访问。