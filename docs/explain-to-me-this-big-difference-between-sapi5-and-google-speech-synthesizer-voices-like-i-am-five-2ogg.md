# 给我解释一下 SAPI5 和谷歌语音合成器语音之间的巨大差异，比如我五岁。

> 原文：<https://dev.to/jochemstoel/explain-to-me-this-big-difference-between-sapi5-and-google-speech-synthesizer-voices-like-i-am-five-2ogg>

传统的 Windows SAPI5 语音合成声音是特定于语言的。声音被设计/录制成特定的语言。这意味着，如果你把英语文本输入到法语语音中，它会像法语一样读和发音，听起来很白痴。

谷歌也有语音合成服务，可以在 Chrome 浏览器和 API 中使用，它的表现非常不同。如果你给一个荷兰的谷歌语音输入英语文本，它会正确地发音，但带有浓重的荷兰口音。这对于传统的文本到语音转换是不可能的。

请描述这两个过程，并向我解释它们之间的根本区别。

谢谢！