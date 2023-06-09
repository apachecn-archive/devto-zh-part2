# JavaScript 怪癖

> 原文：<https://dev.to/peacestone/javascript-quirks-474d>

最近我在蒙特利尔参加了一个 JavaScript 会议，我了解到一些有趣的 JavaScript 怪癖，并决定写下其中的一些。

## 用对象模板文字插值

有了模板文字，我们可以通过使用`${}`符号在模板中插入表达式。一个有趣的例子是，如果我们插入一个对象或数组进行插值，那么在该项上调用`.toString()`方法。在下面的代码中，我们插入了一个空的对象文字。结果是一样的，就好像我们叫`.toString()`一样。

```
const a = {};
a.toString();
// results '[object Object]'

`${{}}`
// results: '[object Object]' 
```

当谈到插值时，需要记住的重要一点是，它实际上是在表达式上调用`toString()`方法的一种简写。一个更奇怪的情况是，如果我们在一个数组中调用`toString()`(或者它的简写`${}`)，而这个数组中有 null 和 undefined。结果是一个没有任何内容的数组。

```
`${[null,undefined]}`
//results: [,] 
```

## 标签

标签允许我们命名 JavaScript 块或循环，以便在`continue`或`break`语句中引用它。在下面的例子中，我将 if 语句的块命名为 fooLabel。然后，我可以通过使用引用 fooLabel 块的`break`语句来跳出 fooLabel 块。这就是第二个 console.log 没有被执行的原因。

```
fooLabel:
    if (true) {
        console.log('started executing block')
        break fooLabel;
        console.log("this won't be executed")
    } 
```

如果我们有一个标记块嵌套在另一个标记块中，我们可以使用标签来指定我们想要从哪个块中断或继续。在下面的例子中，我写了一个带有标签循环的嵌套 for 循环。我现在可以指定我想继续到哪个块:

```
let variable = 'a let variable';
    barLoop:
        for(let i = 0; i < 3; i++){
            fooLoop:
                for(let j = 0; j < 3; j++){
                        continue barLoop
                        variable = 'this line will never be executed'
                }
            }
console.log(variable) 
```

一般来说，JavaScript 代码中不使用标签， [MDN 的关于标签的页面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)有一个注释，说明这种情况并不常见，通常会返回一个函数来跳转循环。

## ParseInt 函数用一串字母

我已经使用`parseInt`将数字字符串转换成整数。我不知道的是，如果基数设置在 10 以上(在可选的第二个参数中)，它还会考虑用字母来表示 10 及以上的数字。如果字母超出范围，它将返回 NaN。如果超出范围的字母与在范围内的字母在一起，它将评估第一个字母并忽略超出范围的字母。下面举几个例子:

```
parseInt('a', 16);
//results: 10

parseInt('b', 16);
//results: 11

parseInt('g', 16);
//results: NaN. The letter g is out of range for our set radix.

parseInt('foo', 16);
//results: 15\. The letters oo our out of range and are ignored. 
```

如果基数设置为 25，字符串 foo 将被转换为数字 9999。这是因为字母 o 现在有了意义，代表了数字 24。

```
parseInt('foo', 25);
//results: 9999 
```

整洁！

我怀疑我是否会写这种类型的代码，但知道这一点很重要，以防我找到它。