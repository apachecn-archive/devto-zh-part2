# 如何在后端框架中实现数据结构和算法以达到 O(log(n))运行时间？

> 原文：<https://dev.to/oathkeeper/how-can-we-implement-data-structures-and-algorithms-in-backend-frameworks-to-reach-ologn-run-time--4jep>

后端框架/平台，如 Node.js(EcmaScript)和 Django(Python)。

例如，我确实认为调用 SomeClass.objects.all()。filter()将循环整个数据(O(N)线性方式),直到得到完整的结果。我认为，如果数据保持在小到中等的程度是没问题的，但是对于大数据来说；我不这么认为。想象一下每天有数百万这样数据和数千个请求。我们如何有效地处理这些请求

*   如果需要，我可以在 Django 中调用原始 SQL(假设我正在使用 Django ),这样可以更高效地请求大量数据

*   我也相信这点。然而，如果我需要这样做，首先我需要适当地组织 sql 中的数据(使用数据结构技术，如哈希表或平衡二叉树)，然后我可以使用算法快速检索它们。我问的是“我想的对吗？”“我想完成的事情是正确的，可以实现的吗？”"大公司会用那种方式实现他们自己的 DS 和算法吗？"我看到很多人分别教授 DS 和算法，并谈论了很多关于它们和它们的力量。但我从没见过两者都被老师在任何框架下讨论和实现过！！

从我所看到的，我开始相信没有人真的在乎，我不知道为什么？

*   作为一名计算机科学专业的毕业生，我总是主要通过在线评委来研究 DS 和算法，我不知道如何将我的知识融入其中？

> 我希望我的问题听起来不模糊或不相关我正处于学习阶段，所以请忽略任何不成熟。感谢任何解释/建议/指导。