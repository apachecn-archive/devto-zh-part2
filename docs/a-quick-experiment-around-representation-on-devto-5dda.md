# 关于开发到的表示的快速实验

> 原文：<https://dev.to/seankilleen/a-quick-experiment-around-representation-on-devto-5dda>

嗨伙计们！

我是 dev.to 社区的忠实粉丝，我喜欢这个地方的想法和潜力。

我注意到的一件事是，在 dev .上的许多帖子中，以短语`Hey guys`或`Hi guys`或`I have a question for you guys`开头。

但是，事情是这样的——这可能有点问题，因为:

*   它以人和他们的经历为中心。当我们使用这个短语时，男人是默认的。如果你怀疑这种情况，想象一下对同一群人说“嘿，女士们！”。如果你在大约 50%的时间里不这样做，可能会有不平衡。
*   它会让人感到被排斥，即使没有排斥任何人的意图。对于不认为自己是“男人”的人来说，科技通常是一个不受欢迎的行业。让他们的经历变得简单一点，或者甚至表示我们重视社区中其他人的经历和包容，这会杀死我们所有人吗？(不，答案是不，不会)。

## 一些替代“伙计”的例子

*   嗨伙计们->嗨大家好！
*   怎么了伙计们？->大家好吗？
*   怎么样了伙计们？->大家好吗？
*   给你们的问题->我有一个关于社区的问题

我发现“乡亲们”对我的肌肉记忆真的很好，所以我主要坚持这一点，尽管我经常以“大家好”开始电子邮件。

## 说“你们这些家伙”并不能说明你是坏人！

这个实验的目的不是羞辱任何人。如果你使用这个短语，没有人说你是一个邪恶的人，也没有人说不使用这个短语的人是无可非议的。我知道很多人，包括女性，都默认了这一点。我有时还是会犯错。它发生了。

## ...但是改变它可以让每个人都变得更好，而不需要付出任何代价。

这是一个伟大的社区；我们当然希望看到它对所有参与其中的人来说更加美好。我发现改变这种肌肉记忆对我有好处，因为它提醒我自己的缺点是什么。我也得到了至少两个注意到这一点的人的感谢，所以我的个人经历说这至少有一些小影响。

## 那么有什么实验呢？

我将对使用“嘿伙计们”或“你们这些家伙”这样的短语来指代社区的帖子发表评论，内容如下:

> 嘿！我注意到在这篇文章中你用“伙计们”来指代整个社区，这个社区不仅仅是由伙计们组成的，而是由各种各样的社区成员组成的。我正在进行一项实验，希望你能参与。你会考虑把“男生”换成一个更包容的称呼吗？如果你对此持开放态度，请让我知道你什么时候改了它，我会删除这个评论。欲了解更多信息和一些替代建议，请参见[帖子链接]。感谢您的考虑！

希望这能激发人们快速编辑或学习更多，甚至多花一秒钟思考表现。如果是这样，我就称之为净赢。

## 附加阅读上“伙计”默认为一群人

*   [“‘嘿，伙计们’的问题”——《大西洋月刊》](https://www.theatlantic.com/family/archive/2018/08/guys-gender-neutral/568231/)
*   [“为什么我终于确信是时候停止说‘你们这些家伙’了”——Vox](https://www.vox.com/2015/6/11/8761227/you-guys-sexism-language)
*   [“你们几个？”-示教公差](https://www.tolerance.org/magazine/you-guys)

## “我喜欢这个！”

厉害！如果你遇到类似的情况，请随意使用我上面的例子评论，或者修改它并链接回这篇文章。

## “这个网站是关于科技的！”

好吧，这是一些代码。

我用 [artoo.js](https://medialab.github.io/artoo/) 查看搜索结果。我点击了 bookmarklet，将其插入到 dev.to 中，然后在我的浏览器控制台中，我输入:

```
artoo.scrape("div.article-engagement-count", { link: {sel: 'a', attr: 'href'}, count: {sel: 'a'} }, artoo.savePrettyJson) 
```

Enter fullscreen mode Exit fullscreen mode

这给了我一个很好的方法来梳理搜索结果，以接触到使用这个短语的更受欢迎的帖子(与按顺序点击每个链接相比，这是一个很大的改进！)

我还使用了一些 powershell 将 json 转换成 CSV 文件:

```
((Get-Content  -Path  "hey-guys.json")  |  ConvertFrom-Json)  |  Export-CSV  "hey-guys.csv"  -NoTypeInformation 
```

Enter fullscreen mode Exit fullscreen mode

从那里，我很容易在 Excel 中打开，并按大多数交互进行排序(我可能做得更快，但这似乎足够简单。)

## “男生是不分性别的！”

实际上不是。这是默认的。这并不意味着中立。它将一个组置于默认位置。这绝不是中立的，用词很重要。这种思维方式的人可能已经被屏蔽了这种默认语言的影响，所以他们可能会感知到一些有利于他们的中性语言。这是正常的人类本能；这是不对的

## “没什么大不了！”

我不是说这是世界末日。我是说每一点都有帮助。

## “你这么做只是为了点击量！”

不，我在这方面做得很好。我只是想让这个世界变得更美好。没必要受欢迎。

如果你真的担心这一点，我邀请你完全复制这篇文章，让它成为你自己的，并说“受到肖恩的启发”。你甚至不需要包括我的姓。保证！

## “吼！你是政治正确/审查/控制！社会公正！布拉！”

这篇文章是为那些有同情心的人写的，他们想改善人们在社区中的体验。

如果你在阅读后感觉与上面的标题相似，你不是这篇文章的目标读者，因为你可能对自己的经历比对别人的感受更感兴趣。请随意忽略这一点，但我希望有一天你会考虑它。