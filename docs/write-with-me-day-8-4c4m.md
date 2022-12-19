# 和我一起写(第八天)

> 原文：<https://dev.to/matt24ray/write-with-me-day-8-4c4m>

欢迎来到第八天的跟我一起写挑战。上周早些时候，我问 Dev.to 社区，他们是否可以给我关于 Elixir 的最佳工具提示和指南，结果没有让我失望。

[![matt24ray image](img/e7c47783dc1319d52f0f08caf9282848.png)](/matt24ray) [## 学习灵丹妙药的最佳资源

### matt 24 ray Jul 27 ' 181min read

#help #elixir](/matt24ray/best-resources-for-learning-elixir-467f)

有很多可靠的资源。现在我有很多工作要做，我没有借口不学仙丹。我在 DailyDrip 的大部分时间都在维护我们的时事通讯，但是能够得到我的工作和开发社区的支持来尝试和学习 Elixir 真是太好了。

今天，我需要把一个句子中的所有字母都小写。通常我会去一个网站，把我需要小写的段落放进去，然后按下按钮，瞧。

然而，今天是不同的。我的直觉是在 iex 会话中完成这个小任务。

所以，我打开了 iex。

```
λ iex
Interactive Elixir (1.6.6) - press Ctrl+C to exit (type h() ENTER for help)
iex> 
```

Enter fullscreen mode Exit fullscreen mode

输入我需要小写的字符串，然后把它赋给一个变量。

```
iex> words = "These Are Words"
"These Are Words" 
```

Enter fullscreen mode Exit fullscreen mode

然后，我错猜调用了' String.lowercase '，没用。

```
iex> String.lowercase(words)
** (UndefinedFunctionError) function String.lowercase/1 is undefined or private. Did you mean one of:

      * downcase/1
      * downcase/2

    (elixir) String.lowercase("There Are Words") 
```

Enter fullscreen mode Exit fullscreen mode

但是...等等。再次阅读该错误...它告诉你你可能想做什么。厉害！让我们试试 downcase。

```
iex> String.downcase(words)
"these are words" 
```

Enter fullscreen mode Exit fullscreen mode

它工作了。厉害！谢谢 iex！