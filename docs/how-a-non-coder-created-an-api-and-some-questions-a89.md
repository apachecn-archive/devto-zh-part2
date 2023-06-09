# 非编码人员如何创建 API(以及一些问题)

> 原文：<https://dev.to/aninditabasu/how-a-non-coder-created-an-api-and-some-questions-a89>

API 让我着迷。你可以 ping 一个未知的系统，并让它与你分享它的宝藏，供你摆弄，这是我觉得非常有趣的事情。我用过的第一个 API 是 Twitter APIs 和 IBM Watson API；我完全爱过(爱？)他们。

我着迷的另一件事是吲哚学。但是，API 和 Indology 并不符合。尽管在古印度有大量免费的数字资源，但这些资源都不能提供数据(宝藏？)以可被应用和程序使用的方式保存在其中。

我有书，有数据。但我不知道如何制作 API(我只用过它们来制作机器人和应用程序)。因此，我四处寻找一个简单的解决方案，并找到了 sheetlabs.com。因此诞生了[吠陀 API](https://aninditabasu.github.io/rigVeda/index.html)。

我想这就是非编码人员制作 API 的方式。在这篇文章中，我想问你们程序员几个问题(同时也大声说出我的 API，因为我对它们感到非常兴奋:)。

### 问题 1

嵌套数组和列表是一个好主意，还是不好？
我的 API 被设计成这样返回 JSON 数据，例如:

```
[
    {
        "mandal": 1,
        "sukta": 2,
        "sungby": "poet_name_1",
        "sungfor": "god_name_1",
        "meter": "meter_name_1"
    },
    {
        "mandal": 1,
        "sukta": 2,
        "sungby": "poet_name_2",
        "sungfor": "god_name_1",
        "meter": "meter_name_1"
    },
    {
        "mandal": 1,
        "sukta": 2,
        "sungby": "poet_name_2",
        "sungfor": "god_name_2",
        "meter": "meter_name_1"
    },
    {
        "mandal": 1,
        "sukta": 2,
        "sungby": "poet_name_2",
        "sungfor": "god_name_3",
        "meter": "meter_name_2"
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

他们这样是不是更好？而且，为什么会这样？

```
[
    {
        "mandal": 1,
        "sukta": 2,
        "sungby": ["poet_name_1", "poet_name2"],
        "sungfor": ["god_name_1", "god_name2", "god_name_3"],
        "meter": ["meter_name_1", "meter_name_2", "meter_name_3", "meter_name_4"]
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 问题 2

我可以用什么样的低成本设置来托管我自己的包含数据表的数据库服务器，然后通过 web 调用使它们可用？我考虑过的一个选择是用 Raspberry Pi 来托管 PostgreSQL 服务器，并开放数据库供 API 调用。可取？其他选择？

### 问题 3

把数据库表转换成 API 的步骤是什么？愚蠢的问题？

我在 Mahabharat 上有大量的数据，我想把它们转换成 API，但是电子表格会超出 sheetlabs.com 的免费限制。另外，一个工作表中的一些列可以链接到另一个工作表中的列。我正在寻找一个更长久的解决办法，所以...想法？