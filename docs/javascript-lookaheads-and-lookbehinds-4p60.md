# Javascript 前视和后视

> 原文：<https://dev.to/wangonya/javascript-lookaheads-and-lookbehinds-4p60>

正则表达式(也称为 regex)是用于匹配字符串中字符组合的模式。它们帮助我们以一种非常高效的方式处理弦乐。

通过用特殊的语法表示正则表达式，您可以:

*   在字符串中搜索文本
*   替换字符串中的子字符串
*   从字符串中提取信息

如果这一切对你来说都是全新的，请看一下 [mdn web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) 了解更多信息。

在这篇文章中，我将重点介绍使用 regex 的一种更简单(也非常有用)的方法。

#### Lookaheads:根据后面的内容匹配一个字符串

##### 格式:`x(?=y)`

`x(?=y)`仅当“x”后跟“y”时才匹配“x”。
我们来看一个例子:

```
// check to see if we have the right 'Kanye'
// /Kanye(?= West)/ : the string 'Kanye' must be followed by 'West'

/Kanye(?= West)/.test('I heard Kanye will be performing tonight') // false. we cant really be sure it's the right Kanye
/Kanye(?= West)/.test('I heard Kanye East will be performing tonight') // false. Kanye who???
/Kanye(?= West)/.test('I heard Kanye West will be performing tonight') // true 
```

Enter fullscreen mode Exit fullscreen mode

你也可以做`/Kanye(?= West | East)/`来匹配 Kanye，如果它后面跟着‘East’或者‘West’。

##### 格式:`x(?!y)`

`x(?!y)`执行逆运算，仅当‘x’是**而不是**后跟‘y’时才匹配‘x’。这被称为否定的前瞻。

```
// we want a different 'Kanye'
// /Kanye(?! West)/ : the string 'Kanye' must not be followed by 'West'

/Kanye(?! West)/.test('I heard Kanye will be performing tonight') // true. might be West, but I'll just take the risk and see
/Kanye(?! West)/.test('I heard Kanye East will be performing tonight') // true. let's give the new guy a chance
/Kanye(?! West)/.test('I heard Kanye West will be performing tonight') // false 
```

Enter fullscreen mode Exit fullscreen mode

#### Lookbehinds:根据前面的内容匹配字符串

**这是一个 [ES2018](https://github.com/tc39/proposal-regexp-lookbehind) 的特色**🎉🎊🚀🎸🤘🏾

##### 格式:`(?<=y)x`

`(?<=y)x`仅匹配前面带“y”的“x”

```
// check to see if we have the right 'Kanye West'
// /(?<= Kanye) West/ : the string 'West' must be preceded by 'Kanye'

/(?<= Kanye) West/.test('I heard West will be performing tonight') // false. we cant really be sure it's the right West 
/(?<= Kanye) West/.test('I heard Keith West will be performing tonight') // false 
/(?<= Kanye) West/.test('I heard Kanye West will be performing tonight') // true 
```

Enter fullscreen mode Exit fullscreen mode

##### 格式:`(?<!y)x`

`(?<!y)x`仅当**不是**前面有“y”
时才匹配“x”

```
// check to see if we have another 'West'
// /(?<= Kanye) West/ : the string 'West' must be not be preceded by 'Kanye'

/(?<! Kanye) West/.test('I heard West will be performing tonight') // true 
/(?<! Kanye) West/.test('I heard Keith West will be performing tonight') // true 
/(?<! Kanye) West/.test('I heard Kanye West will be performing tonight') // false 
```

Enter fullscreen mode Exit fullscreen mode

就成了😄。Regex 可能有点难掌握，但是一旦你掌握了，你会发现它使处理字符串变得容易多了。让我知道你使用 regex 的其他一些很酷的方式。