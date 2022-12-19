# 在 R 和 Python 之间选择...通过询问语言本身

> 原文：<https://dev.to/adamspannbauer/choosing-between-r-and-python-by-asking-the-languages-themselves-2klh>

多年来，在统计/数据科学领域，关于 R 和 Python 有很多讨论。如果你刚开始从事数据科学，你应该选择哪种语言？哪种语言更擅长统计？机器学习用哪种语言比较好？哪种语言更擅长 ______？

当然，一些非常棒的人已经尽力帮助你回答这些问题。例如，你可以在[数据营](https://www.datacamp.com)查看 Karlijn Willems 分享的[这张信息图](https://www.datacamp.com/community/tutorials/r-or-python-for-data-analysis)。该分析通过许多不同的类别，并为其读者提供了硬数据。这篇文章不会做这些。

今天我们要问的是哪种语言更好。如果他们都同意谁是赢家，这将为我们提供一个简单的答案。

### 询问 R

```
>  'R'  >  'python'  [1]  TRUE 
```

### 问蟒

```
>>> 'python' > 'R'
True 
```

良好的...看起来我们还在原地。没有明显的赢家可以为您或您的组织解决数据科学问题。两种语言在不同的情况下都很棒，而且两种语言背后都有令人惊叹的开源社区。

* * *

## 稍微解释一下为什么比较会不同

正如标题所说，这将只是一个*小点*。对于 R，你可以查阅[对比文档](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Comparison.html)；细节部分讨论字符串比较。对于 Python，你可以看看 Python 大师[的这篇文章中的字符串比较部分。](https://thepythonguru.com/python-strings/)

在这篇文章中，我们将忽略比较的细节和原因。我们将对所有的字母和数字进行排序。这向我们展示了如果我们使用不同的用例或替换来编写比较，结果会有所不同。

### 在 R 中排序字符

```
>  # create and sort a vector of letters & digits  >  sorted_chars  =  sort(c(LETTERS,  letters,  0:9))  >  collapsed_sorted_chars  =  paste(sorted_chars,  collapse='')  >  >  print(collapsed_sorted_chars)  [1]  "0123456789aAbBcCdDeEfFgGhHiIjJkKlLmMnNoOpPqQrRsStTuUvVwWxXyYzZ" 
```

### Python 中的排序字符

```
>>> import string
>>>
>>> # create and sort a list of letters & digits >>> sorted_chars = sorted(string.ascii_letters + string.digits)
>>> collapsed_sorted_chars = ''.join(sorted_chars)
>>>
>>> print(collapsed_sorted_chars)
'0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz' 
```