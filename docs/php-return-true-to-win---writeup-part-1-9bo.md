# PHP:向 win - WriteUp 返回 true(第 1 部分)

> 原文：<https://dev.to/antogarand/php-return-true-to-win---writeup-part-1-9bo>

# 简介

是一个包含 16 个 PHP 挑战的网站，我们必须在给定的上下文中使用最少的字符返回 true。

这些挑战展示了 PHP 的许多有趣的特点，并且难度变化很大，这就是为什么解决和理解这些问题可以帮助我们更好地理解这门语言。

这篇文章将涵盖前 8 个解决方案，而即将到来的第 2 部分将涵盖其余的。

现在是时候停止阅读，在阅读解决方案之前尝试自己解决挑战了！

网址: [https://returntrue.win](https://returntrue.win/)

## 一级

#### 挑战

```
function foo($x)
{
    return $x;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(!0); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

显而易见的解决方案是输入`true`，但那将是 4 个字节，最短的答案只用了其中的一半！我们可以通过使用`!0`来节省两个字节。
之所以`!0`行得通是因为[逻辑不(！)](https://secure.php.net/manual/en/language.operators.logical.php)运算符将[类型的值先强制转换](https://secure.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting)为布尔值，并返回与此相反的结果。

随着`0`转化为`false`，我们得到`!false`，也就是`true`。

参见:[布尔类型转换](https://secure.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting)获取转换为 false 的值列表。

## 二级

#### 挑战

```
function foo($x)
{
    return $x('gehr') === 'true';
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(str_rot13) 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

我们的第一个猜测可能是创建一个总是返回`'true'`的[匿名函数](https://secure.php.net/manual/en/functions.anonymous.php)，如下:

```
foo(function(){return'true';}) 
```

Enter fullscreen mode Exit fullscreen mode

但这不是最短的解决方案。

最短 9 字节分数的关键是赋予函数的‘gehr’参数，它正好是每个字母移动 13 个字符的`true`。

事实证明，php 有 [str_rot13](https://secure.php.net/manual/en/function.str-rot13.php) 函数来执行这个精确的操作！

PHP 不喜欢崩溃，它通过聪明的转换来做到这一点。
当我们使用一个未定义的常数时，会使用以下行为:

> 如果你使用一个未定义的常量，PHP 会假设你指的是常量本身的名字，就像你以字符串的形式调用它一样(CONSTANT vs“CONSTANT”)。
> From: [PHP 常量语法](http://us.php.net/manual/en/language.constants.syntax.php)

将其与[变量函数](https://secure.php.net/manual/en/functions.variable-functions.php)结合

> 这意味着，如果一个变量名后面附加了括号，PHP 将寻找一个与该变量的求值结果同名的函数，并尝试执行它。

我们已经有了一个可行的解决方案！

## 三级

#### 挑战

```
function foo($x)
{
    return ($x >= 1) && ($x <= 1) && ($x !== 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(!0)
// Or 
foo(1.) 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这里的第一个要求是得到一个变量，它的值`<=`和`>=`大于 1。
由于这些是数字比较，PHP 会将等式的两边都转换成数字。

第二个要求是我们的变量不能是整数`1`。

转换为数字的布尔值将设法通过以下条件:

> FALSE 将产生 0(零)，TRUE 将产生 1(一)。

来自 doc: [整数:来自布尔](https://secure.php.net/manual/en/language.types.integer.php#language.types.integer.casting.from-boolean)

的造型这给了我们第一个答案，通过`true`将成功通过所有检查。

第二种解决方案基于 PHP 对不同数字类型的处理。

你可能知道，PHP 既有[浮点数](https://secure.php.net/manual/en/language.types.float.php)又有[整数](https://secure.php.net/manual/en/language.types.integer.php)。

由于类型不同，`1.0`和`1`对不同的运算符返回 true。

因此我们的解是`1`的浮点值，可以写成`1.`

引用:
[比较运算符](https://secure.php.net/manual/en/language.operators.comparison.php)

[整数:由布尔型](https://secure.php.net/manual/en/language.types.integer.php#language.types.integer.casting.from-boolean)转换而来

## 四级

#### 挑战

```
function foo($x)
{
    $a = 0;
    switch ($a) {
        case $x:
            return true;
        case 0:
            return false;
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(0); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

由于`$a`是`0` an，我们需要程序流进入我们的`case $x`，解决方法是将`$x`设置为`0`。

这里没什么挑战性！

## 五级

#### 挑战

```
function foo($x)
{
    return strlen($x) === 4;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(💩); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这里棘手的部分是获得最短的解决方案，它只有 1 个字符长，而`strlen`将返回 4。
解决方案在于`strlen`和`mb_strlen`之间的差异，它们以不同的方式处理多字节字符串:

> strlen()返回字符串中的字节数，而不是字符数。
> 
> [mb_strlen()]多字节字符计为 1。

由于 utf8 支持每个字符最多 4 个字节，我们的便便表情符号在`strlen`函数上返回`4`，尽管只有一个字符。

参考资料:
[strlen 文档](https://secure.php.net/manual/en/function.strlen.php)

[mb_strlen 文档](https://secure.php.net/manual/en/function.mb-strlen.php)

## 六级

#### 挑战

```
function foo($x)
{
    return $x === $x();
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
// Not working anymore, 22 characters
foo(($x=session_id)($x).$x);
// Shortest? working solution: 33 characters
foo($x=function()use(&$x){return $x;})
// Alternative working solution, 42 characters
foo($GLOBALS[x]=function(){return$GLOBALS[x];})
// Runner up, 44 characters
foo(new class{function __invoke(){return$this;}}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这是最有趣的挑战之一，我花了很多时间寻找最短的解决方案。

请注意，在撰写本文时，由于网站上做了额外的沙盒，最短的解决方案将**无法**工作，但如果您启用了会话，它确实可以在本地工作。

这里我想探讨四种不同的解决方案。

##### 最短

```
($x=session_id)($x).$x
// Which is equivalent to
$x = 'session_id';
$x($x);
foo($x); 
```

Enter fullscreen mode Exit fullscreen mode

首先，这个解决方案的本质是 [session_id](https://secure.php.net/manual/en/function.session-id.php) 函数。

该函数用作 getter/setter:

*   当不带参数调用时，它将返回存储的值。
*   当使用参数调用时，它将分配其内部值并返回一个空字符串。

这个解决方案的第一部分是将`$x`变量设置为`'session_id'`(作为字符串，因为 [PHP 处理未定义的常量](http://us.php.net/manual/en/language.constants.syntax.php))。

第二部分是调用 session_id，多亏了 [PHP 的变量函数](https://secure.php.net/manual/en/functions.variable-functions.php)，用`$x`作为参数，它再次工作。由于`$x`是一个字符串，这将把`session_id`的内容设置为`'session_id'`。

我们`session_id($x)`调用的结果是一个空字符串，这不是我们想要发送给`foo`的。

为了将`session_id`发送到`foo`，我们将`$x`连接到我们之前拥有的空字符串，这产生了`'session_id'`字符串。

最后，foo 函数将比较我们的入口变量`$x`，aka。`'session_id'`，对`$x()`的结果，又名。`session_id()`。
因为我们将`session_id`设置为`'session_id'`，所以两个字符串是相同的，我们通过了比较！

##### 自动返回功能使用使用

```
$x=function()use(&$x){return $x;} 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案非常简单:创建一个使用自身并返回自身的函数。

参考:[匿名函数](https://secure.php.net/manual/en/functions.anonymous.php)，具体为[例#3](https://secure.php.net/manual/en/functions.anonymous.php#example-165)

##### Globals

```
$GLOBALS[x]=function(){return$GLOBALS[x];}
// Which is equivalent to

$GLOBALS[x] = function() {
    return $GLOBALS[x];
};
foo($GLOBALS[x]); 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案与前一个类似，但是使用了 [$GLOBALS](https://secure.php.net/manual/en/reserved.variables.globals.php) 数组，而不是从父作用域继承变量。

##### 返回自身的可调用类

```
new class {
    function __invoke()
    {
        return $this;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从 PHP7 开始，我们可以创建[匿名类](https://secure.php.net/manual/en/language.oop5.anonymous.php)。

这让我们创建一个新类，实现了 [__invoke](https://secure.php.net/manual/en/language.oop5.magic.php#object.invoke) 魔法方法。

这个神奇的方法让我们在调用对象作为解决挑战的函数时返回`$this`！

## 七级

#### 挑战

```
function foo(stdClass $x)
{
    $x = (array) $x;
    return $x[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo((object)[!0]); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这个解决方案在于 PHP [类型如何转换成对象](https://secure.php.net/manual/en/language.types.object.php#language.types.object.casting)。

当我们将一个带有`[0 => true]`的数组类型转换为一个对象时，我们创建了一个带有`0`属性和`true`值的通用标准类。

一旦这被用`$x = (array) $x;`行类型强制转换回一个数组，我们对象的属性`0`回到新数组的索引 0。

## 八级

#### 挑战

```
class Bar {}

function foo(Bar $x)
{
    return get_class($x) != 'Bar';
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(new class extends Bar{}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这个解决方案依赖于 PHP 中的[对象继承](https://secure.php.net/manual/en/language.oop5.inheritance.php)。

我们可以创建一个新的子类，它将`Bar`扩展为一个参数。由于给定对象是一个新类， [get-class](https://secure.php.net/manual/en/function.get-class.php) 将返回新匿名类的名称，而不是“Bar”。

# 结论

这些都是应对挑战的原创解决方案！大多数解决方案都产生了警告和通知，因此大多数奇怪的行为应该在适当的开发环境中被检测到。

我要感谢:

*   [@matthieunapoli](https://twitter.com/matthieunapoli) 制作了这个很棒的网站
*   感谢你在我处理困难的时候解决了最简单的挑战
*   [@rpkamp82](https://twitter.com/rpkamp82) ，感谢他在 returntrue.win 上的[报道](https://www.rpkamp.com/2018/02/15/all-answers-to-returntrue.win-with-explanations/)
*   他的[要点](https://gist.github.com/odan/0799dfa59d40acdb18e8a1fa9611a996)包含许多简短的解决方案

如果你知道类似的怪事或挑战网站，请把它们发给我，这样我就可以解决并写下它们了！

# 参考文献

[https://return true . win](https://returntrue.win)
T3】https://gist . github . com/odan/0799 DFA 59d 40 acdb 18 E8 a1 fa 9611 a 996
[https://www . rpk amp . com/2018/02/15/all-answers-to-return true . win-with-explainions/](https://www.rpkamp.com/2018/02/15/all-answers-to-returntrue.win-with-explanations/)
[【https://alf.nu/ReturnTrue](https://alf.nu/ReturnTrue)-JavaScript 的类似网站