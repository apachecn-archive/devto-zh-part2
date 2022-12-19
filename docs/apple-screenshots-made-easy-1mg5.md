# 苹果截图变得简单

> 原文：<https://dev.to/pinwheeler/apple-screenshots-made-easy-1mg5>

我尽量不在我的桌面上存储很多东西，当我有各种其他窗口时，它不是一个方便我存储任何我可能为了快速发送电子邮件而拍摄的截图的地方。我想要一种方法，能够点击并拖动我最近的截图到我可能正在处理的任何东西中。为此，我研究了如何改变默认的截图位置，发现多亏了《OSX 日报》,这非常容易。出于我的目的，我使用了桌面上名为 snapshot s 的文件夹的位置，但是您可以将任何内容放在指定的文件路径中。非常酷的部分是，这个截图文件夹是坐在我的码头，并按照日期添加排序。这意味着我可以只按 CMD-SHIFT-4 和 badda-bing，badda-boom 就有了一个可以使用的截图。想做同样的事吗？只需打开终端，键入以下内容:

`defaults write com.apple.screencapture location ~/Desktop/Screenshots/`
T1】

如果您想切换回常规路径，可以键入下面的
`defaults write com.apple.screencapture location ~/Desktop/` killall SystemUIServer `命令

附加提示:将文件夹添加到你的 dock 中，并按添加日期排序。您最近的截图只需点击一下鼠标