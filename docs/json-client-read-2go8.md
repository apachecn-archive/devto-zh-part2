# Json 客户端读取

> 原文：<https://dev.to/vjoxyodo/json-client-read-2go8>

在我的开发者生涯中，从来没有想到会同时有多个客户端出现这种情况。

几个月前，我开发了一个与我们的系统进行数据集成的 API，该 API 总是返回一个带有两个键(输出和消息)的 json 数据结构模式。消息键可以有多个字符串输出，但输出总是 0(不成功)或 1(成功)。

两周前，我添加了一个名为 guid 的新键，它包含一个 uuid(通用唯一标识符)，例如。482 b8a 20-8108-11e 8-ADC 0-fa7a e 01 bbe BC，该机具不会因输出和/或信息而堵塞。突然，客户说他们的输出比实际的多(顺便说一句，我跟踪所有的输出)。所以愚蠢的客户端不进行解析，而是在 json 模式中进行字符串搜索，例如，如果出现如下内容:

{"output": 0，" message ":"有事"，" tx guid ":" 482 b8a 20-8108-11e 8-ADC 0-fa 7 AE 01 bbebc " }

它们将识别为 1，因为该模式在 json 模式中至少有一个 1。

我以为我什么都见过，但这绝对是第一次。

页（page 的缩写）的 API 文档对 json 输出很明确！