# 一个更简单的 javascript 标记器

> 原文：<https://dev.to/albertywu/an-even-simpler-javascript-tokenizer-anb>

你所知道的在 javascript 中标记算术表达式的最简单的方法是什么？假设您正在构建一个计算器应用程序，并希望这种情况发生:

```
console.log(
 tokenize('100-(5.4 + 2/3)*5')
)
// ['100', '-', '(', '5.4', '+', '2/3', ')', '*', '5'] 
```

Enter fullscreen mode Exit fullscreen mode

在您使用 npm 模块之前，请认识到这可以通过使用 string [split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) 方法的一个秘密特性在一行 javascript 中完成。请看:

```
'100-(5.4+2/3)*5'
  .split(/(-|\+|\/|\*|\(|\))/)
  .map(s => s.trim())
  .filter(s => s !== '')
// ['100', '-', '(', '5.4', '+', '2/3', ')', '*', '5'] 
```

Enter fullscreen mode Exit fullscreen mode

打扰一下。`split`函数内部的热混乱是什么？让我们用几个复杂性逐渐增加的例子来一步一步分解它:

* * *

#### 例一:`s.split(/-/)`

显而易见:这将在字符串`s`看到减号符号`-`的地方进行分割。

```
'3-2-1'.split(/-/)
// ["3", "2", "1"] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 例二:`s.split(/(-)/)`

与前一个示例的唯一不同之处是正则表达式中的封闭参数，它创建了一个捕获组。下面是整篇文章的关键点:如果正则表达式包含分隔符周围的捕获括号，那么每次匹配分隔符时，捕获组的结果都被拼接到输出数组中。

```
'3-2-1'.split(/(-)/)
// ["3", "-", "2", "-", "1"] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 例三:`s.split(/(-|\+)/)`

通过增加对加法符号`\+`的支持，这构建了前面的例子。需要反斜杠`\`来对正则表达式进行转义。竖线`|`充当 OR 语句(匹配`-`或`+`)。

```
'3-2-1+2+3'.split(/(-|\+)/)
// ["3", "-", "2", "-", "1", "+", "2", "+", "3"] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 最终 Boss(把一切绑在一起)

希望你现在已经拥有了理解`.split(/(-|\+|\/|\*|\(|\))/)`所需的所有工具。希望这有意义！如果你喜欢这篇文章，请在评论中告诉我，或者在 [twitter](https://twitter.com/albertywu) 上联系我！