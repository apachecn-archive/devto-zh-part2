# 与终端交换世界杯的贴纸图形

> 原文：<https://dev.to/adambrandizzi/exchanging-sticker-cards-with-the-terminal-2ki9>

在最近的世界杯期间，我的爱好之一是收集贴纸。事实上，我已经建立了贴纸相册，因为我的儿子想要它，但我也很开心，我想。

<figure>[![2018 sticker album showing France team missing three pictures.](img/ff7d399762bd56caf5d85e65c4019b77.png)](https://i2.wp.com/suspensao.blog.br/descrenca/wp-content/uploads/2018/07/IMG_20180716_090001719.jpg) 

<figcaption>可惜，还没完成</figcaption>

</figure>

收集贴纸的一个重要环节就是交换重复的。通过 WhatsApp 群里的消息，报告我们有哪些重复贴，哪些还需要。作为一个程序员，我拒绝自己比较列表，所以我写了一个[小程序](https://pastebin.com/FLDz684P) em Python(带 [doctests 和所有的](https://dev.to/adambrandizzi/give-doctest-a-chance-4d2g))来寻找交集。

### 失踪的笔记本电脑

上周，一个人来我家交换贴纸。我有重复的和需要的卡片的清单，有我的也有她的，但是我的脚本在另一台笔记本电脑里。我甚至不知道这台机器在哪里，我的客人很着急。

没有时间找到电脑，也没有时间重写程序。或者甚至手动比较。

Unix 时代到了！

### 列表格式

一般来说，这些清单的格式如下:

`15, 18, 26, 31, 40, 45 (2), 49, 51, 110, 115, 128, 131 (2), 143, 151, 161, 162, 183 (2), 216 (2), 221, 223, 253, 267 (3), 269, 280, 287, 296, 313, 325, 329, 333 (2), 353 (3), 355, 357, 359, 362, 365, 366, 371, 373, 384, 399, 400, 421 (2), 445, 457, 469, 470, 498 (2), 526, 536, 553, 560, 568, 570, 585, 591 (2), 604 (2), 639 (2), 660.`

基本上，我需要删除所有不是数字的内容，以及括号中的数字，并比较两个列表。很简单，真的。

### 用 sed 进行预处理

首先，我必须删除括号中的计数器:

`$ cat list.txt | sed 's/([^)]*)//g'
15, 18, 26, 31, [...] 591 , 604 , 639 , 660.`

(我知道， [UUOC](http://porkmail.org/era/unix/award.html) 。随便啦。)

然后，我把每个数字放在自己的行中:

`$ cat list.txt | sed 's/([^)]*)//g' | sed 's/, */\n/g'`

后来，我清理了每一行，删除了所有不是数字的字符:

`cat list.txt | sed 's/([^)]*)//g' | sed 's/, */\n/g' | sed 's/[^0-9]*\([0-9]*\)[^0-9]*/\1/g'`

实际上，我只调用`sed`一次，传递两个表达式。在这里，我相信多次调用`sed`会更清楚。)

最后，我对值进行排序:

`$ cat list.txt | sed 's/([^)]*)//g' | sed 's/, */\n/g' | sed 's/[^0-9]*\([0-9]*\)[^0-9]*/\1/g' | sort -n > mine-needed.txt`

我用需要的贴纸列表和重复的贴纸列表来做，得到两个文件。

### 用 grep 寻找交集

现在，我需要比较它们。有很多选项，我选择[用`grep`](https://stackoverflow.com/a/27960271/287976) 。

在这种情况下，我通过`-f`选项调用了`grep`，其中一个文件作为输入，另一个文件作为匹配的模式列表。此外，这里只有完整匹配才重要，所以我们将使用`-x`标志。最后，我让`grep`用`-F`标志直接比较字符串(而不是把它们当作正则表达式)。

`$ fgrep -Fxf mine-needed.txt theirs-repeated.txt
253
269
333
470
639`

搞定了。一分钟，我已经知道我想要哪种贴纸了。我只需要重复做同样的事情。

### 这个为什么有意思？

今天，这些俏皮话对我来说真的没什么大不了的。有趣的是，当我开始使用终端时，它们会令人难以置信。真的，看看我们用了多少管道来预处理文件！还有这个`grep`绝招？我只是创建了一个有效的正则表达式！实际上，在解决这个问题之前，我甚至不知道`-x`选项。

我曾经帮助一个朋友处理了大量的文件。他已经花了两个多小时尝试用 Java 来做，我们一起用 shell 脚本十分钟就解决了。然后他问我他有多想知道 shell script，问我怎么学。

好吧，小例子(就像这个)，看起来很简单，却教会了我很多。我就是这样学习编写脚本的:尝试解决问题，小批量地了解新的命令和选项。最后，这是一项宝贵的技能。

所以，我希望这个小游戏也能丰富你的一天。我当然丰富了我的——我想在花三倍的时间写 Python 脚本之前考虑一下！

<small>*此贴是[Trocando figurinhas sobre o terminal](http://suspensao.blog.br/descrenca/trocando-figurinhas-sobre-o-terminal/)的翻译。*</small>