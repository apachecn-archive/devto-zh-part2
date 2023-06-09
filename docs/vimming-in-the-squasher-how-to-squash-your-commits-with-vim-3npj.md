# 如何用 VIM 粉碎你的提交

> 原文：<https://dev.to/kahlil/vimming-in-the-squasher-how-to-squash-your-commits-with-vim-3npj>

每当我使用`git rebase -i`挤压提交时，Git 就会在 VIM 中打开 squasher(这就是我刚刚为交互式 rebase 命名的文本视图)。

我对 vimming 的了解不多，所以我过去常常只是键入`i`，然后用箭头键一点一点地移动，一个字符一个字符地删除，以便多次删除单词“pick ”,然后多次键入“squash”。

当然，这非常烦人，所以我最终在 squasher 中查找了如何 vim，这很棒。所以你应该这样做:

首先，移动到您想要压缩的第一个提交所在的行，通常是第二个提交。

然后，进入一种叫做*的视觉屏蔽模式* (WTF？！这到底是什么意思？)通过点击`ctrl` + `V`。神奇的事情发生了:当你移动光标时，它会用白色挡住你移动的每个字符，如果你向下移动，它会覆盖你在上面一行中覆盖的所有字符。看起来像积木。所以我想这就是“视觉障碍”的由来😅。

选择所有您想要挤压的行，同时在视觉上遮挡单词“pick”。

现在，更神奇的是:点击`C`并键入单词“squash ”,然后点击`esc`,看到单词“squash”应用于所有“视觉上被屏蔽”的地方。

如果您只能在此时退出 VIM，那么您实际上可以成功地粉碎所有这些提交。一个男孩会做梦，对吗？