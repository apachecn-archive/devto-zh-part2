# 恩梅蒂项目

> 原文：<https://dev.to/4lch4-industries/project-enmeti-o0g>

*封面照片由[吴镇男·安妮斯](https://unsplash.com/@dcanies)在[Unsplash](https://unsplash.com)T5 拍摄*

## 我的第一个真正的 VSCode 扩展

在我的帖子中，我谈到了开始#100DaysOfCode 挑战，我提到了我可能要做的事情之一是对 Visual Studio 代码进行扩展，这将帮助我将链接插入到我的 Markdown 文件中。这在我写帖子的时候特别有用，比如这篇，我在我的机器上写了一个 Markdown 文件，然后将内容复制/粘贴到[开发者到](https://dev.to)或任何我要发布的其他网站。

考虑到这个过程是如此简单和重复，我觉得这样做特别烦人:

1.  选择要转换为链接的文本。
2.  在它周围加上`[`括号。
3.  添加一个[x + 1]引用，其中 x 是我添加到帖子的最后一个链接的编号。
4.  将引用添加到文件的底部，旁边有链接。

虽然不难，但如果你曾经自动化/简化过一个平凡的任务，那么你就会知道我所说的那种感觉。

## 输入 Enmeti

这就把我带到了今天，实际上我已经把代码发布到了网上的一个 [GitHub repo](https://github.com/HF-Solutions/Enmeti) 中。到目前为止，这个扩展只有一个`extension.insertLink`命令，它会用括号将您选择的文本括起来，并在它的末尾添加一个`[0]`引用。退一步说，没什么特别的😅然而，据我所知，这是一个好的开始，因为我已经有了编辑当前选中文本的基础，而且我在开始编辑时不知道如何做*。*

 *到目前为止，我设法完成的工作主要归功于 Visual Studio 代码扩展的 [yo 生成器](https://code.visualstudio.com/docs/extensions/yocode)、T2 的 MDTools 示例和 T4 的 VSCode API 文档。所以，非常感谢那些参与这些项目的人。

这就是我今天所知道的，随着我的进展，我一定会发布更多的更新😊*