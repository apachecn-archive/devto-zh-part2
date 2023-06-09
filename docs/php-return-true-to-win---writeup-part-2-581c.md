# PHP:向 win - WriteUp 返回 true(第 2 部分)

> 原文：<https://dev.to/antogarand/php-return-true-to-win---writeup-part-2-581c>

# 简介

欢迎来到 [Returntrue.win](https://returntrue.win/) 报道的第二部分！

希望你已经阅读并理解了这些挑战的前半部分。虽然不是必须的，但建议先在这里读一下[！](https://dev.to/antogarand/php-return-true-to-win---writeup-part-1-9bo)

现在是时候停止阅读，在阅读解决方案之前尝试自己解决挑战了！

网址: [https://returntrue.win](https://returntrue.win/)

## 9 级

#### 挑战

```
function foo($x)
{
    $y = $x + 1;
    return ++$x != $y;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(x); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这里的解决方案在于 PHP 如何实现[增量](https://secure.php.net/manual/en/language.operators.increment.php)操作符。

虽然您可能希望`++$x`在转换之前将`$x`转换为数字，但它实际上会返回字母表中的下一个字符。

在我们的例子中，`++$x`等于`y`，因为它是跟在`x`后面的下一个字母，
`$y`将是一个数字，又名。`1`，因为它是加法的结果。

因此最后的比较是`'y' != 1`，这是真的！

## 十级

#### 挑战

```
class Bar
{
    private $a;

    public function __construct($a)
    {
        $this->a = (bool) $a;
    }

    public function a()
    {
        return $this->a;
    }
}

function foo(callable $x)
{
    $object = new Bar(false);
    $x($object);
    return $object->a();
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(function(&$a){$a=new$a(1);}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这个很棘手，也很有趣！

下面是这个答案的长而不统一的版本:

```
foo(function(&$barInstance) {
    $barInstance = new Bar(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

由于我们在函数中被给定了参数`$object`，使用它作为[引用](https://secure.php.net/manual/en/language.references.pass.php)可以让我们修改它。

我们不能轻易修改这个对象的`a`属性，因为它是私有的，但是我们可以将这个变量重新赋值为 Bar 的一个新实例！

缩小版使用了一些技巧来缩短有效载荷:

*   在构造函数中使用`1`而不是`true`，因为`Bar`构造函数将元素转换为布尔值
*   从实例[创建一个类的新实例，如这里所记录的](https://secure.php.net/manual%20%20/en/language.oop5.basic.php#language.oop5.basic.new#example-174)
*   删除所有不必要的空格和缩进

这里最短有效负载最有趣的部分是使用`new $a(1)`创建一个新的`Bar`实例，这是我在阅读解决方案之前不知道的！

## 11 级

#### 挑战

```
function foo(array $x)
{
    return $x[0] === null;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo([]); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

如果访问数组，未定义的偏移量将返回 null，因此空数组将通过此检查。

## 12 级

#### 挑战

```
function foo($x)
{
    return $x[123456] !== null
        && $x[123456] === $x[123457];
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(x); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

这乍一看似乎很奇怪，但答案是传递任何字符串。

访问一个字符串上的无效字符索引将返回一个空字符串`''`，它通过所有条件。

它相当于下面的数组，只不过打印出了额外的通知:
`[123456 => '', '']`

##### 引用:

[StackOverflow:寻找数组的下一个数字索引](https://stackoverflow.com/a/3698786/5399585)

## 13 级

#### 挑战

```
class Test
{
    private $foo = true;
}

function foo($x)
{
    $o = (array) new Test;
    return $o[$x];
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo("\00Test\00foo"); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

解决方案在于知道[数组转换](https://secure.php.net/manual/en/language.types.array.php#language.types.array.casting)是如何工作的。

> 键是成员变量名，有几个明显的例外:
> 
> ...]私有变量在变量名前面有类名；
> 
> [...]这些前置值的两边都有空字节。
> 
> 由于`foo`是`Test`的私有属性，其数组键为`Testfoo`，`Test`，`foo`前为空字节。

为了获得最短的 11 分，您需要在提交的请求中用一个真正的*null byte 替换两个转义 null byte`\00`。

我将把这作为一个挑战留给读者！*

## 14 级

#### 挑战

```
function foo($x)
{
    return is_object($x) && is_object($x());
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(function(){yield;}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

一旦解释清楚，解决方案就很简单，但要找到它确实很难。

首先需要了解的是，匿名函数是由 PHP 中的[闭包](https://secure.php.net/manual/en/class.closure.php)类表示的。

这让我们使用比创建一个可调用对象更少的字符来传递第一个`is_object($x)`。

第二个需要的知识是 PHP 的[生成器](https://secure.php.net/manual/en/language.generators.syntax.php)。

`yield`关键字返回一个生成器，它可以用于以后继续执行。

这就引出了这个非常简短的 18 个字符的解决方案！

## 15 级

#### 挑战

```
function foo($value)
{
    return $value != $value;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo(NAN); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

根据 IEEE754 规范， [NAN](https://secure.php.net/manual/en/math.constants.php) 常量不等于自身。

更多细节关于[这个 stackoverflow](https://stackoverflow.com/questions/1565164/what-is-the-rationale-for-all-comparisons-returning-false-for-ieee754-nan-values) 的问题。

## 16 级

#### 挑战

```
function foo($x)
{
    // This level is slightly different than level 12
    // the comparison is != instead of !== ;)
    return $x[123456] != null
        && $x[123456] === $x[123457];
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 解

```
foo([123456=>1,1]); 
```

Enter fullscreen mode Exit fullscreen mode

#### 解释

与挑战 12 不同，松散的等式比较不允许我们使用字符串来传递第一个条件。

这个解决方案有效的原因是 PHP 增加最后插入的数字键来创建它的下一个值。

在这种情况下，由于第一个插入的键是`123456`，第二个将是`123457`，两者的值都是 1。

# 结论

这些都是应对挑战的原创解决方案！大多数解决方案都会产生警告和通知，因此大多数奇怪的行为应该在适当的开发环境中被检测到。

如果你喜欢了解奇怪的行为和挑战，一定要在 Twitter 上关注我，我会在那里发布这类东西

就像我之前的博文一样，我要感谢:

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