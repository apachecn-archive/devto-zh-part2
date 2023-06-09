# 用 Python 学习中文

> 原文：<https://dev.to/lukaszkuczynski/learning-chinese-with-python-38j8>

# [T1】简介](#intro)

我从 2016 年开始学中文。不幸的是，进展很慢，但我不时会接到一些任务去准备。然后我需要用那种语言覆盖一些材料。然后用我的语言(波兰语)或英语和汉字书写文本。我只能勉强认出其中的 20 或 50 个，所以拼音是必须的。因为我每天都在使用 Python，所以我想:让我们看看如何使用它来自动化我的工作。

# Python 很棒

它有各种工具。我只花了几分钟就找到了很好的分词库，将字符组合成单词(领头的是`jieba`)，然后使用拼音音译(`xpinyin`是许多例子中的一个)。这就是在 Python 中完成的简单程度

```
import jieba
from xpinyin import Pinyin

sentence = "我想说更好的中文，但很难，因为我是波兰人"
print(sentence)

segments = jieba.cut(sentence)
output = " ".join(segments)
print(output)

p = Pinyin()
pinyined = p.get_pinyin(output, splitter='', show_tone_marks=True)
print(pinyined) 
```

Enter fullscreen mode Exit fullscreen mode

它将产生输出:

```
我想说更好的中文，但很难，因为我是波兰人
Building prefix dict from the default dictionary ...
Dumping model to file cache /tmp/jieba.cache
Loading model cost 0.899 seconds.
Prefix dict has been built succesfully.
我 想 说 更好 的 中文 ， 但 很 难 ， 因为 我 是 波兰人
wǒ xiǎng shuō gènghǎo de zhōngwén ， dàn hěn nán ， yīnwèi wǒ shì bōlánrén 
```

Enter fullscreen mode Exit fullscreen mode

很酷不是吗？现在我可以用它来处理文件，从而使我的工作更快。