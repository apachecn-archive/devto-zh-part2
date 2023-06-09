# 怎么了医生？

> 原文：<https://dev.to/4lch4-industries/whats-up-doc-53cj>

*封面照片由 [rawpixel](https://unsplash.com/photos/mGVhGkvBTYc) 在[Unsplash](https://unsplash.com/search/photos/update)T5 拍摄*

嗯，我的上一篇博客已经过去很长时间了，所以这是一篇关于我自上次发表以来所做的事情以及我现在的计划的总括，因为假期即将来临。

## [王位](#tron)

当然，我一直在为我的宝贝, [Tron](https://github.com/HF-Solutions/Tron) 工作，修复了一些错误，并添加了一个已经被请求了一段时间的命令:

*   错误修复
    *   [修复了`+adopt`命令](https://github.com/HF-Solutions/Tron/commit/78c7298b6a3475fb85c170fc09ca03721564c43f)按预期工作而不是不回复。
    *   [增加了 Try/Catches](https://github.com/HF-Solutions/Tron/commit/96163b2716b76e01ff56e65cf923b6aeb8b1d9ce) 来帮助防止**权限删除导致 Tron 崩溃的**错误。
    *   [修正了`+reddit`命令](https://github.com/HF-Solutions/Tron/commit/0a430de6a4aacb4d3f58d2305e6bee0c02f0d6d0)，因此它可以正确显示所请求的子编辑中的图像。
    *   [修正了`+birthday`命令](https://github.com/HF-Solutions/Tron/commit/2837581548dd7829e031e24179b8542843be66f8)，所以它可以正确保存生日。
    *   出于某种原因，我忘记了通过客户端，所以它实际上没有保存任何东西。
    *   [修复了`+marriage`命令](https://github.com/HF-Solutions/Tron/commit/35c7b8340e30226845e97035596cd2f7001fd0c9)，这样它就可以接受正确用户的输入。
    *   [修复了`getImage()`函数](https://github.com/HF-Solutions/Tron/commit/bade884f7954a08262374aef3cc92daa03ecb94a)，这样它就能正确检查数值。
    *   [修复了`onCommandRun`事件](https://github.com/HF-Solutions/Tron/commit/bf4745311e233aabde5ce2249cb361445dfed9f7)，所以如果用户试图在 DM 中运行命令，它不会崩溃。
*   增加
    *   [应少数用户要求，增加了清除命令](https://github.com/HF-Solutions/Tron/commit/7a67b9681a4b26d23b35e69a846d724c368ffa31)。

## 恩梅蒂

我设法让 [Enmeti](https://marketplace.visualstudio.com/items?itemName=hf-solutions-llc.enmeti) 达到一个令人尊敬的状态，现在它的版本是 1.1.1。您可以在空白行或光标位置插入链接和/或图像来代替现有文本。我还有一些工作要做，特别是检测文件的底部，但我相信它现在的位置比我之前写的要好得多。

要了解更多信息，请查看 Visual Studio 代码市场上的扩展，或者访问 GitHub 上的[库](https://github.com/HF-Solutions/Enmeti)😊

## 个人/公司网站

很长一段时间以来，[我的公司网站](https://hasslefree.solutions)都托管在 [SquareSpace](https://www.squarespace.com/) 上，直到最近我发现了 [GitHub 页面](https://pages.github.com)的乐趣。在摆弄了一两个星期后，我终于适应了，为 **HassleFree Solutions，LLC** 创建了现在的[我的新网站](https://hasslefree.solutions)。当我完成 **HassleFree Solutions** 网站时，我相信我可以用不同的主题和博客支持来建立自己的[个人网站](https://alcha.org)。我的个人网站使用的是亚历克斯·卡彭特(Alex Carpenter)创作的[材质——杰基尔(Jekyll)——主题](https://github.com/alexcarpenter/material-jekyll-theme)，而我的公司网站使用的是时间机器主题[和我自己做的一点定制，让它看起来更好看一些(imo)。](https://pages-themes.github.io/time-machine/)

## 结论

我需要写更多！虽然我觉得自从我上一次写作以来，我已经做了很多事情，但是当我写下我一直在做的事情时，我感觉并没有那么有成就感。所以，尽管如此，我打算每周至少写一篇文章，并发表它，不管我是否对此感到高兴😅

也就是说，如果您有任何意见、批评或建议，请不要犹豫，让我知道！在我的[个人网站](https://alcha.org/#contact-info)上有很多联系我的方式。