# Elm 对 Exercism 的挑战:Rna 转录

> 原文：<https://dev.to/antonrich/elm-challenge-on-exercism-rna-transcription-1kdh>

我会很短。

我正在做练习。

有一次，我和编译器发生了争执。

* * *

这个`if`的第 4 和第 5 分支产生不同类型的值。

4 | if x = = ' G ' then
5 | ' C '
6 | else if x = = ' C ' then
7 | ' G '
8 | else if x = = ' T ' then
9 | ' A '
10 | else if x = = ' A ' then
11 | ' U '
12 | else
13 |>DNA 序列中有未知字符！！!"

第四分支有这种类型:

```
Char 
```

但是第五个是:

```
String 
```

提示:一个`if`的所有分支需要匹配，这样无论我们
取哪个分支，我们都得到相同类型的值。

* * *

对我来说，这似乎不合理:)

现在，我被困在这里了。

* * *

`toRNA`的定义与其类型注释不匹配。

17 | toRNA:String-> Result Char String
18 | toRNA DNA =
19 |>converted _ to _ list DNA
20 |>|>list . map convert
21 |>|>String . from list

`toRNA`的类型注释说它总是返回:

```
Result Char String 
```

但是返回值(如上所示)是:

```
String 
```

在 1 个模块中检测到错误。

* * *