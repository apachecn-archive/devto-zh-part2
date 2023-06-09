# 练习:制作简单的正则表达式引擎

> 原文：<https://dev.to/aminarria/exercise-making-a-simple-regex-engine-id7>

我已经开始为 [Lambdaclass](http://lambdaclass.com/) 工作，开始了解 Erlang。作为与潜在客户面谈的准备，我得到了一个基于这篇文章的任务:[用不到 40 行代码构建一个 Regex 引擎](https://nickdrane.com/build-your-own-regex/)。所以现在我需要试着用 Erlang 来做，让我们看看效果如何。

## 任务

创建一个函数(`match/2`)，它将接收一个字符串和一个正则表达式(也是一个字符串)，如果匹配就返回`true`，否则返回`false`。您不会使用所有的 Regex，只是它的一个子集，如下所示:

| 句法 | 意义 | 例子 | 比赛 |
| --- | --- | --- | --- |
| `a` | 匹配指定的字符 | `k` | k |
| `.` | 匹配任何字符(换行符除外) | `.` | 甲、乙、丙、丁... |
| `?` | 匹配前一个字符的 0 或 1 | `aq?` | 答，aq |
| `*` | 匹配 0 个或多个前面的字符 | `b*` | ""，b，bb，bbb |
| `^` | 匹配字符串的开头 | `^ca` | 加拿大 |
| `$` | 匹配字符串的结尾 | `eb$` | eb |

## 注意事项

在着手制定解决方案之前，我需要确定一些在设计解决方案时要考虑的因素。

*   给定的正则表达式不会无效(例如:`"^*", "*?"`)
*   我可以忽略换行符的存在，因为我希望输入简单的字符串。因此字符`.`匹配任何东西
*   一个空的正则表达式不匹配任何东西，所以`match(SomeString, EmptyRegex)`将返回`false`
*   只要它匹配了某个东西，我就可以返回 true，因为我不关心在哪里或者是什么。

## 解决问题

制定解决方案的第一步是创建基本案例。空字符串 or 和空正则表达式不能匹配任何内容。

```
-module(regex).

-export([match/2]).

match([], _Regex) ->
  false;
match(_String, []) ->
  false; 
```

Enter fullscreen mode Exit fullscreen mode

下一步是匹配相同的字符，这要归功于模式匹配:

```
match([Char | String], [Char | Regex]) ->
  matching(String, Regex);
match([_Char | _String], [_OtherChar | _Regex]) ->
  false. 
```

Enter fullscreen mode Exit fullscreen mode

这样做的时候，我发现我已经犯了一个错误，我的`match/2`函数可以正确匹配像`match("hi", "hi")`这样的东西，但是不能匹配`match("oh hi", "hi")`。我需要一种方法来尝试匹配字符串和正则表达式，尝试字符串中的每个字符作为起点。为此我修改了`match/2`来调用一个私有的`matching/2`函数。如果它成功了`match/2`将返回 true，但是如果它失败了，我将再次尝试，但是删除字符串的第一个字符。

```
match(String, Regex) ->
  case matching(String, Regex) of
    true ->
      true;
    false ->
      match(tl(String), Regex)
  end. 
```

Enter fullscreen mode Exit fullscreen mode

`matching/2`将通过使用正则表达式和字符串来工作。每当不再有正则表达式时，就找到了匹配。我将添加这个子句并修改现有的字符匹配。

```
matching(_String, []) ->
  true;
matching([], _Regex) ->
  false;

matching([Char | String], [Char | Regex]) ->
  matching(String, Regex);
matching([_Char | _String], [_OtherChar | _Regex]) ->
  false. 
```

Enter fullscreen mode Exit fullscreen mode

对于`.`,我只需要有一个子句来匹配字符串中的任何字符，并继续匹配。

```
matching([_Char | String], [%. | Regex]) ->
  matching(String, Regex); 
```

Enter fullscreen mode Exit fullscreen mode

下一步是处理剩余的特殊字符。我想`^`和`$`会容易些，所以先说它们。对于`^`，我只需要给`match/2`添加一个子句来检查它，并返回`matching/2`返回的任何内容，不能继续尝试。对于`$`，我只需要检查字符串是否为空并返回`true`，如果不为空则返回`false`。

```
match(String, [$^ | Regex]) ->
  matching(String, Regex);

...

matching([], [$$]) ->
  true;
matching(_String, [$$]) ->
  false; 
```

Enter fullscreen mode Exit fullscreen mode

现在开始最难的部分，处理`?`和`*`。一如既往，我认为从简单到困难是最好的，所以让我们从`?`开始。当字符串和正则表达式匹配时，我需要消耗它们的字符，但是当它们不仅仅消耗正则表达式的字符时。

```
matching([Char | String], [Char | [$? | Regex]]) ->
  matching(String, Regex);
matching(String, [_OtherChar | [$? | Regex]]) ->
  matching(String, Regex); 
```

Enter fullscreen mode Exit fullscreen mode

不是最漂亮的图案搭配，但很管用。问题是这给我们留下了一个问题:现在我们可以没有字符串，仍然匹配成功(例如:`match("hi", "hio?")`)。为了解决这个问题，我将添加一个子句来检查`?`是否是下一个字符，如果是，则继续匹配。

```
matching([], [_Char | [$? | Regex]]) ->
  matching([], Regex);
matching([], _Regex) ->
  false; 
```

Enter fullscreen mode Exit fullscreen mode

除了当它匹配时，我们不继续消费它，而是继续调用它，直到它不匹配。像以前一样，我也会遇到没有更多字符串的问题，所以我也需要为此添加一个子句。

最后一个问题:`.*`。这个组合将匹配每一个字符，并防止类似`match("hello", ".*o")`的匹配。这真的很难找到一个好的方法来处理。最终我意识到，由于`.*`无法匹配任何东西，我可以像`match/2`一样使用相同的尝试和消耗字符串的过程。

```
matching(String, [$. | [$* | RemRegex]] = Regex) ->
  case matching(String, RemRegex) of
    true ->
      true;
    false ->
      matching(tl(String), Regex)
  end; 
```

Enter fullscreen mode Exit fullscreen mode

## 结束

最后，经过一番努力(40 多行代码)，我终于完成了一个简单的正则表达式引擎。考虑边缘情况、如何进行最佳模式匹配以及如何对函数子句进行排序真的很有趣。我在下面留下了整个最终模块的一个片段。

PS:如果你有兴趣通过一些练习学习 Erlang，请查看 [Erlings](https://github.com/lambdaclass/erlings)

```
-module(regex).

-export([match/2]).

match([], _Regex) ->
  false;
match(_String, []) ->
  false;
match(String, [$^ | Regex]) ->
  matching(String, Regex);
match(String, Regex) ->
  case matching(String, Regex) of
    true ->
      true;
    false ->
      match(tl(String), Regex)
  end.

matching(_String, []) ->
  true;

matching([], [$$]) ->
  true;
matching([], [_Char | [$? | Regex]]) ->
  matching([], Regex);
matching([], [_Char | [$* | Regex]]) ->
  matching([], Regex);
matching([], _Regex) ->
  false;

matching(_String, [$$]) ->
  false;

matching(String, [$. | [$* | RemRegex]] = Regex) ->
  case matching(String, RemRegex) of
    true ->
      true;
    false ->
      matching(tl(String), Regex)
  end;

matching([Char | String], [Char | [$? | Regex]]) ->
  matching(String, Regex);
matching(String, [_OtherChar | [$? | Regex]]) ->
  matching(String, Regex);

matching([Char | String], [Char | [$* | _RemRegex]] = Regex) ->
  matching(String, Regex);
matching(String, [_OtherChar | [$* | Regex]]) ->
  matching(String, Regex);

matching([_Char | String], [$. | Regex]) ->
  matching(String, Regex);
matching([Char | String], [Char | Regex]) ->
  matching(String, Regex);
matching([_Char | _String], [_OtherChar | _Regex]) ->
  false. 
```

Enter fullscreen mode Exit fullscreen mode