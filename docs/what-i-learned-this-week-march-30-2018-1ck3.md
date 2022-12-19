# 本周(2018 年 3 月 30 日)我学到了什么

> 原文：<https://dev.to/jrop/what-i-learned-this-week-march-30-2018-1ck3>

## 有向图:[连通分量](https://en.wikipedia.org/wiki/Connected_component_%28graph_theory%29)

我开始研究在有向图中寻找连通分量的算法，目的是能够更有效地增加大型依赖图的可见性(特别是在数据库中；例如，作业`J`使用表`T`产生`T'`...).这些信息可用于制定更省时的计划，在我的机构中生成数据。有了如何在我们的依赖图中找到“岛”的想法，我决定研究它，并尝试学习一些关于图论的知识。

我的进一步阅读:[强连通分量](https://en.wikipedia.org/wiki/Strongly_connected_component)是一种确定图中比其他区域更强连通的区域的方法。这看起来很酷。我希望它能有实际用途！

## 重新学习: [DAG:拓扑排序](https://en.wikipedia.org/wiki/Topological_sorting)

关于上面的问题，我实现了我们的依赖图的拓扑排序，以找到所有可以并行运行的作业，从而使我们的调度更加有效。最后:我在工作中使用了某个类的东西！使用曾经是家庭作业的东西总是令人满意的。

## [立竿见影](#libclang)

显然，您可以通过 libclang 库利用为 LLVM 构建的 C frontend。详见[本帖](https://shaharmike.com/cpp/libclang/)。利用这一点，你可以解析 C++，遍历 AST - Great 东西。

甚至还有 [nodejs 绑定](https://www.npmjs.com/package/libclang)。

## RPython

如果你想生产一个支持解释*和*的 VM，一个即时编译，RPython 是相当惊人的。[这篇文章](http://tratt.net/laurie/blog/entries/fast_enough_vms_in_fast_enough_time.html)非常有趣。

**TLDR；** RPython 是一个 VM 生成器:它由一种语言(Python 的一个子集)和一个分析`(interpreter code + hints)`并为你生成一个(基于 C 的)VM(解释器+ JIT)的工具组成。

## 史博勒斯

最近，我作为一名资源加入了我们部门的一个项目，这个项目需要集成单点登录服务 Shibboleth。有趣的事实: [Shibboleth 在圣经](https://biblehub.com/esv/judges/12.htm)(士师记 12:6)中被提及，作为古代以色列人在他们自己的部族敌对期间实施边境控制的一种方式。

Shibboleth 作为一个产品是高度可配置的，是一个半复杂的野兽。虽然我以前有过设置它的经验，但似乎总有其他东西需要学习。我在这方面的大部分学习都是在沮丧中度过的，同时试图让它在一个同样复杂、负载平衡的环境中工作。