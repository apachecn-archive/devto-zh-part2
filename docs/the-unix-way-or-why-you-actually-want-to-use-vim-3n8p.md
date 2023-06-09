# Unix 之路...或者为什么您实际上想要使用 Vim

> 原文：<https://dev.to/gypsydave5/the-unix-way-or-why-you-actually-want-to-use-vim-3n8p>

我喜欢 [Dev 到](https://dev.to/t/vim/top/infinity)上的[所有](https://dev.to/vintharas/exploring-vim-4k1i)[Vim](https://dev.to/hamza/vim-for-starters---the-minimum-you-need-to-know-3ob)[教程](https://dev.to/webdevchallenges/summary-a-brief-introduction-to-vim-gcf)。他们很棒！它们是很好的起点。请去把它们都读完！

但是...

我想告诉你所有维姆的秘密教学。有点跑题了。你知道，你在少林第三十六宫或其他地方得到的东西，因为出于某种原因，我们喜欢将我们所有的软件开发隐喻与武术和/或禅宗混合在一起。 <sup id="fnref1">[1](#fn1)</sup>

让我告诉你 Unix 的方式...

## Unix 哲学

不，我不打算解释什么是 Unix。你可以谷歌一下。这里有一个到维基百科“Unix 哲学”页面的链接，这个页面很长，所以让我们直奔主题吧，嗯？

用古代大师道格拉斯·麦克洛伊的话说:

> 写程序做一件事，并把它做好。

说起来容易做起来难。如果你想做不止一件事怎么办？

## 管道

管道是 Unix 系统的瑰宝。对我来说，这是至高无上的荣耀。你把一个程序的输出作为另一个程序的输入——就像把一些软管连接在一起，让一股水流通过。

假设我想在某个地方找一个包含“鲍勃”的随机单词。在一台类似 Unix 的机器上，你会发现一个有很多单词的文件——一本字典。Mac 和 BSD 系统在`/usr/share/dict/words`都有。

我们可以用程序`cat`在终端显示:<sup id="fnref2">[2](#fn2)</sup>T5】

```
cat /usr/share/dict/words 
```

但是现在我们需要找到所有包含“bob”的单词...那么，我们*可以用`grep`* 打开文件吗

```
grep bob /usr/share/dict/words 
```

但是我发现使用一个*管道*将`cat`的输出传递到`grep`更直观，我们用管道符号:`|` <sup id="fnref3">[3](#fn3)</sup>
来写它

```
cat /usr/share/dict/words | grep bob 
```

这就给了我所有包含“bob”的单词——我如何随机选择一个呢？有几种方法可以做到这一点，但这里有一个:我的电脑上有一个程序 [`shuf`](https://en.wikipedia.org/wiki/Shuf) ，它会打乱你发送的行:

```
cat /usr/share/dict/words | grep bob | shuf 
```

现在我只想要其中一句台词——哪一句都没关系。为什么不只是第一个呢？ [`head`](https://en.wikipedia.org/wiki/Head_(Unix)) 就可以了——它可以只给我们输入的第一行:

```
cat /usr/share/dict/words | grep bob | shuf | head -1 
```

看哪

```
> cat /usr/share/dict/words | grep bob | shuf | head -1
bobolink
> cat /usr/share/dict/words | grep bob | shuf | head -1
nabobish 
```

那是什么意思？我可以使用一些其他程序来帮助——我可以`open`(至少在 OSX)使用一个叫做 [`parallel`](https://www.gnu.org/software/parallel/) :
的便捷程序，找到一个包含那个单词的网址

```
cat /usr/share/dict/words | grep bob | shuf | head -1 | parallel open https://en.wiktionary.org/w/index.php?title={} 
```

然后...然后...然后...

重要的教训是:我们没有编写新的程序。我们没有下载新的随机 bob 单词生成器。我们所做的是使用一系列做一件事(并且做好一件事)的程序，然后把它们粘在一起，得到我们想要的东西。

## 但是你答应过我们 Vim！

是的，是的。维姆。自从我开始开发以来，我就一直使用 Vim 我曾经用它来写营销邮件，让写营销邮件变得更有趣一点(因此也更容易忍受)。我们总是谈论 Vim 中的按键绑定——`hjkl`的魔力——但是现在你几乎可以将它们添加到任何文本编辑器中。

那么，我认为 Vim 的优点是什么？

这:

```
:%! 
```

对于我们当中的 Vim 新手:`:`表示我们正在输入一个命令。`%`是对当前缓冲区中所有行的引用。而`!`表示我们将在 shell 中运行一个命令。

上面的代码将通过一个 shell 命令来处理当前的缓冲区，并替换其中的内容。试试看！

```
:%! shuf 
```

这很有趣，尽管极具破坏性。但是它可以更有用。想要重新格式化缓冲区中的所有 JSON 吗？ [`jq`](https://stedolan.github.io/jq/) 是你的朋友:

```
:%! jq '.' 
```

或者您可能希望 Python 做同样的事情:

```
:%! python -m json.tool 
```

以 HTML 格式查看一些减价商品？试试 [pandoc](http://pandoc.org/) :

```
:%! pandoc 
```

您可以用一系列的行或者一个可视的选择来完成它——和 Vim 一样，帮助是可用的:

```
:h ! 
```

现在，你可以利用其他人的程序来扩展你的编辑器*，而不需要*安装额外的修改或插件，或者像使用 VSCode、Atom 或 Sublime 那样使用新的编辑器。如果您发现了一些有用的东西，并反复使用它，您可以编写一些 VimL 并使命令更容易运行。

另一个优点是——通过学习 Unix 环境中一些简单的、面向做一件事的程序，您将能够在自己编写的越来越多的程序中反复使用它们。

## 交给你了

当你遇到一个你没有工具解决的问题时，这变得非常真实。对我来说，就是需要把一些大型 JavaScript 对象翻译成 JSON 的时候。一个我在编辑 <sup id="fnref4">[4](#fn4)</sup> 中看不到容易完成的问题。

所以我写了一个简短的 NodeJS 脚本,它接受一个 JavaScript 流并输出一个 JSON 流。不美，但有效。

写了一次之后，通过一个管道友好的接口，我现在可以对 Vim 缓冲区中的一个 JavaScript 对象这样做:

```
:%! js2json 
```

还是在壳里:

```
cat some.js | js2json | curl -X POST -d @- http://gimme-json.com 
```

或者也许我再也不会用它了。

但它就在那里。我通过利用编辑器与我编写的程序集成的能力来扩展我的编辑器。谢谢 Vim！谢谢 Unix！

## Vi-Nature 就是 Unix-Nature

Vim 之所以伟大，不是因为它让你的朋友对你深奥的击键、 <sup id="fnref5">[5](#fn5)</sup> 的知识印象深刻，也不是因为你的精英触摸打字技能让你可以在不离开键盘的情况下每秒钟打出 x-city-x-billion 个字符。这些都是有趣的事情，但它们是肤浅的事情。我们不应该沉迷于它们，也不应该因为了解它们而沾沾自喜。

Vim 很棒，因为它无处不在——如果您了解 Vim/vi，那么您就可以在大多数 Unix 系统上使用它的编辑器。这很好，因为您可以将它集成到 Unix 工具中——这很好，因为您可以自己编写这些工具，因为它们并不复杂*而且您是一名程序员*！

你也会在其他编辑器中看到同样的想法。用大师 Wq 的话说:

> Vim 不是永久的。nvi 不是永久的。vi 本身不是永久的，只有 VI-自然。Emacs 有 vi-nature，nano 有 vi-nature，连记事本都有 vi-nature。你缩小你的视野，你变得依恋...].你必须离开。等你掌握了 Emacs 再回来。 <sup id="fnref6">[6](#fn6)</sup>

在这里您可以用简单的 Emacs 命令做同样的事情:

```
C-x h C-u M-| js2json 
```

黑客快乐！

* * *

1.  全是忍者和公案，或者是木工和“手艺”。 [↩](#fnref1)

2.  严格来说这是[对`cat`T2 的滥用。](http://harmful.cat-v.org/cat-v/) [↩](#fnref2)

3.  想法是麦克洛伊，但符号是肯·汤普森和 T2。想象一下使用它第一天——管道热！ [↩](#fnref3)

4.  解决这个问题的一个困难的方法是编写一个宏。那将是一次性的，而且可能很耗时。 [↩](#fnref4)

5.  你们现在都必须尝试`ggg?G`*。 [↩](#fnref5)*

**   只缺两个编辑器，你应该都试试:`ed`和`acme`。 [↩](#fnref6)*