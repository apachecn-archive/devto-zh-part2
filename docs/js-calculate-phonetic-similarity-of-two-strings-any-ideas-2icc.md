# 计算两个字符串的语音相似度，有什么想法吗？

> 原文：<https://dev.to/jochemstoel/js-calculate-phonetic-similarity-of-two-strings-any-ideas-2icc>

我正在研究一个自然语言处理引擎。它使用谷歌语音 API 来转录简短的录音，然后解释器试图理解它。

我现在想计算两个字符串之间的语音相似度，最好用 javascript。不要比较两个音频文件，因为单词或短语来自不同的说话者。

为了举例说明，牛肉和 T2 叶听起来 87%相同。*聋*和*穴*这两个词大概占 29%。(构成百分比)

介于 0 和 1 之间的值显然也可以。一样的区别。

有什么想法吗？这是不可能的吗？