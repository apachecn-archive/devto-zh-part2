# 如何改进这个 while 循环代码片段？

> 原文：<https://dev.to/seanballais/how-can-i-improve-this-while-loop-code-snippet-2o4n>

我有这段代码。

```
while (!Character.isWhitespace(this.source.charAt(this.currentCharIndex))) {
    char c = this.source.charAt(this.currentCharIndex);
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

它所做的基本上就是遍历一个字符串，直到遇到一个空白字符。

但是，我觉得代码有点乱。我该如何改善它？

我可以试试这段代码。

```
char c = this.source.charAt(this.currentCharIndex);
while (!Character.isWhitespace(c)) {
    c = this.source.charAt(this.currentCharIndex);
} 
```

Enter fullscreen mode Exit fullscreen mode

它看起来更干净，但`this.source.charAt(this.currentCharIndex)`似乎是多余的(与之前的片段相同)，所以我需要你的意见，你会如何处理这个问题。

谢谢！:D