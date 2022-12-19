# 用 Python 自动化简单的事情是很棒的

> 原文：<https://dev.to/rpalo/automating-simple-things-with-python-is-awesome-3jlg>

蒂姆·伊斯利在 Unsplash 上拍摄的封面照片。

这只是一个关于我昨天工作中发生的事情的快速帖子。

于是我就这样了:作为一名机械工程师努力工作。你知道，测量东西。眯着眼看东西。做些笔记。印度求购各种螺栓和螺钉。老样子。

我刚刚下载了一些我们要在夹具中使用的夹具的 3D 模型文件——真的，抱歉，软件测试人员。[这个夹子](https://www.destaco.com/catalog/215-U)，其实。当文件进来时，我吓得直喘气。这些文件都有错误的扩展名！我点击按钮下载了一个满是`.sldprt`(单个零件)和`.sldasm`(组件)文件的文件夹，但是它们是以`.prt.1`和`.asm.1`文件的形式出现的。怎么回事！？更糟糕的是，有几个 XML 文件被扔进了这个混合体——对我来说毫无用处！

所以我右键单击并重命名第一个文件，更改为适当的扩展名。当我右击第二个文件(大约 30 个)时，我内心的开发者开始变得暴躁。“我们没必要忍受这些！”他喊道，把他的小键盘扔过他的小办公室。(我内心的开发者创业公司有办公室和门，因为我不是怪物。)

不管怎样，回到他身上。“我们没必要忍受这些！”他哭了。“我们不是一些无脑的，推纸的无人机！我们可以编程！我们马上就能写好剧本！”

“我们*可以*，”我内心的项目经理插话说。(我内心的创业公司人员充足。)“但这真的能为我们节省多少时间呢？我们可以在两分钟内完成对所有这些文件的重命名。”

“好吧，”内心的开发者回答，下唇开始固执地突出。那我就在 120 秒或更短的时间内完成。"

他接着说，“废话。我们在没有 Bash 的 Windows 机器上，我不太记得操纵字符串的 PowerShell 语法了。或者，至少，不足以及时完成。哦，等等！我们知道 Python！我们可以做任何我们想做的事情！”

因此，他很快引导我去寻找一种遍历目录和重命名文件的方法。我必须搜索，因为我知道有几种方法可以做到这一点。是`os`，还是`shutil`，还是`pathlib`？我认为任何一个都可以。我知道有很多方法可以做我想做的事情，但是我需要一种可以在 120 秒内完成的方法，结合谷歌搜索和实施。

啊哈！明白了！

“那就少了 30 秒，”我内心的项目经理警告道。“赶快行动！”

所以我打开了一个终端，启动了`ipython`，我最喜欢的 Python REPL。(是的我用过`bpython`。是的，这是惊人的。我还是喜欢`ipython`。)

```
import os

for filename in os.listdir("."):
    if filename.endswith(".1"):
        basename = filename[:-6]
        extension = filename[-5:-2]
        os.rename(filename, basename + ".sld" + extension) 
```

Enter fullscreen mode Exit fullscreen mode

我的内部开发团队击掌并碰杯庆祝。

这是我写过的最漂亮、最优雅的代码吗？没有。但我在 120 秒内完成了吗？是的。成功了吗？是的。我觉得自己像个巫师吗？你说得太对了。

Python 很牛逼，不管我用它做什么，都让我觉得很开心。

## 片尾场景

现在我不在时钟上，我可以自由地花我喜欢的时间，我决定我更喜欢这个解决方案:

```
from pathlib import Path

for file in Path(".").glob("*.1"):
    name, ext, _ = file.name.split(".")  # There were no other .'s in the names
    file.rename(f"{name}.sld{ext}") 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/11/03/automating-with-python-is-cool/)*