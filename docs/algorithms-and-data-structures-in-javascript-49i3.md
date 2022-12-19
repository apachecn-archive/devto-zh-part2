# JavaScript 中的算法和数据结构

> 原文：<https://dev.to/trekhleb/algorithms-and-data-structures-in-javascript-49i3>

读者你好！我最近在 GitHub 上发布了 JavaScript [算法和数据结构](https://github.com/trekhleb/javascript-algorithms)知识库，其中收集了在 ES6 JavaScript 中实现的经典算法和数据结构，并附有解释和进一步阅读材料和 YouTube 视频的链接。还有[算法和数据结构 YouTube 播放列表](https://www.youtube.com/playlist?list=PLLXdhg_r2hKA7DPDsunoDZ-Z769jWn4R8)，其中包含该存储库中提到的所有视频，所以你可以去参加这个手工制作的在线学习课程:)

所以我想你已经掌握了这个项目的主要思想— **帮助开发者学习和练习算法，并用 JavaScript** 来完成。

为了让这个过程更加顺畅，我试着为每个算法和数据结构添加了一些图形说明，这样可以让算法背后的思想更容易理解和记忆。

您还可以在 root README 文件中找到一些实用信息，这些信息在您学习时可能会很方便。以下信息:

*   **大 O 符号图** —快速查看 O(n！)或者说，O(n)
*   **列出一些最常用的大 O 符号**及其性能比较——了解 10 有多大！(是 3628800)
*   **数据结构操作复杂性** —不同数据结构的搜索、读取或插入速度有多快
*   **排序算法复杂度对比表** —帮助您根据自己的情况选择合适的排序算法

[![Big O graphs](../Images/f58c57a0ddb5440e2802f51811c58743.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NQZMO44u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/whmiuox9wmrf1szrlg1a.png)

所有的**代码都被测试**100%覆盖。这样做不仅是为了保持代码正确工作，也是为了说明每个算法或数据结构是如何工作的，它们有哪些基本操作(比如对堆的轮询)，以及什么是边缘情况(如果图是有向的，该做什么)。

仓库也有一个操场。这只是一个很小的函数模板和一个空的测试用例，它将帮助您在克隆 repo 后立即开始测试或处理算法。

目前，涵盖了以下数据结构:

*   链接列表
*   长队
*   堆
*   散列表
*   许多
*   优先队列
*   特里
*   树(二叉查找树，AVL 树)
*   图形(有向和无向)
*   不相交集

在这些数据结构之上，有超过 50 种流行的算法被实现。其中包括排序、搜索算法、图形/树/集合/字符串/数学相关算法。所有算法也按其范例分类:

*   **强力算法** —查看所有的可能性并选择最佳解决方案
*   贪婪算法(Greedy Algorithms)——选择当前时间的最佳选项，不考虑未来
*   **分治算法** —将问题分成更小的部分，然后解决这些部分
*   **动态规划算法** —使用先前找到的子解决方案构建解决方案
*   **回溯算法**——类似于蛮力试图生成所有可能的解，但每次生成一个解时都要测试它是否满足所有条件，然后才继续生成后续的解。否则，走回头路，走上另一条寻找解决方案的道路

[JavaScript 算法和数据结构](https://github.com/trekhleb/javascript-algorithms)存储库仍在积极开发中，更多的算法和数据结构即将推出。您也可以通过贡献您的代码和 web 已知算法的实现成为其中的一部分！

希望这个资源库对你有帮助！享受编码！