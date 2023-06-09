# Perl 5 中常量的字符串插值

> 原文：<https://dev.to/kirklewis/string-interpolation-of-constants-in-perl-5-181o>

这篇文章演示了如何在字符串插值和正则表达式中使用 Perl 5 常量。我将简要解释字符串插值在 Perl 5 中是如何工作的，特别是对于常量。最后，我解释了如何在正则表达式中使用常量，并给出了一个用例。

## 字符串插值

在 Perl 5 中，任何嵌入在双引号中的标量或列表变量都会在运行时被替换为它们的值。这种类型的字符串被称为*插值字符串*，它在一个被称为[字符串插值](https://en.wikipedia.org/wiki/String_interpolation)的过程中被解析。

*举例:*

```
my $PI   = 3.14;
my @nums = (3, 2, 1);

say "scalar: $PI - list: @nums"; 
```

Enter fullscreen mode Exit fullscreen mode

以上输出:

```
scalar: 3.14 - list: 3 2 1 
```

Enter fullscreen mode Exit fullscreen mode

或者，Perl 5 中的内插字符串可以包含一个包装在`${}`或`@{}`中的*标识符*，根据它们的类型，它们得到相应的解析——在本例中分别是*标量和列表*

*举例:*

```
say "scalar: ${ PI } - list: @{ nums }"; 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
scalar: 3.14 - list: 3 2 1 
```

Enter fullscreen mode Exit fullscreen mode

在上面的字符串中，Perl 查找并找到当前名称空间中的每个标识符*，即* `PI`和`nums`，并解析它们。

> *由于表达式`${}`和`@{}`更清楚地强调了插值的意图，我将继续使用它们。*

然而，用 Perl 5 `constant` prama 创建的常数是无符号的，因此将一个常数插入双引号字符串中，将不会被解析。见下图:

```
use constant DEV_OPS => 'dev_ops';
my $status = 'online';

say "Team ${ DEV_OPS } is ${ status }"; 
```

Enter fullscreen mode Exit fullscreen mode

以上结果会产生以下输出:

```
Global symbol "$DEV_OPS" requires an explicit package name... 
```

Enter fullscreen mode Exit fullscreen mode

发生上述情况是因为与`$status`不同，常量`DEV_OPS`没有符号，所以 Perl 无法查找并解析它- *关于引号" "*

## 解析常数

为了使插入的字符串知道要处理的任何常量，使用反斜杠`\`来引用它，如下所示:

```
say "Team ${ \DEV_OPS } is ${ status }";
# or if we use more than one constant
say "Team ${ \DEV_OPS } - ${ \DEV_OPS } is ${ status }"; 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Team dev_ops is online
Team dev_ops - dev_ops is online 
```

Enter fullscreen mode Exit fullscreen mode

### 列出上下文

常量值在列表上下文中计算，所以`[]`也可以用来引用它们。然后使用合适的插值表达式如下:

```
say "Team @{ [DEV_OPS] }..."; 
```

Enter fullscreen mode Exit fullscreen mode

但是，我发现用反斜杠`\`更简单。

* * *

## **在正则表达式中使用常数**

假设你有三个常数，每个常数代表一种不同的颜色，如下:

```
use constant BLUE  => 'blue';
use constant GREEN => 'green';
use constant RED   => 'red'; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你有一个颜色列表`@colors`，你想用上面列出的三种颜色来验证它，你可以这样写:

```
my  @colors = qw(blue yellow orange red);
my  $colors_regexp = qr/^(blues|green|red)$/; # typo intended...

say $_ =~ $colors_regexp
    ? "$_ is valid"
    : "$_ is invalid" foreach @colors; 
```

Enter fullscreen mode Exit fullscreen mode

以上输出:

```
blue is invalid
yellow is invalid
orange is invalid
red is valid 
```

Enter fullscreen mode Exit fullscreen mode

现在`@colors`中的`blue`实际上是有效的，但是正则表达式中的错别字`blues`导致匹配失败。虽然这种打字错误是故意的，但它也可能因失误而发生，并被完全忽略。

**解决方案**

因为每种颜色都被定义为一个常量，所以使用每种颜色来组成正则表达式更安全。这和我之前解释的方法一样，通过使用反斜杠`\`来引用每个常量。

```
# lets make the regular expression a constant also
use constant COLORS_REGEXP => qr/^(
    ${\BLUE}  |
    ${\GREEN} |
    ${\RED}
)$/x; 
```

Enter fullscreen mode Exit fullscreen mode

> *为了让正则表达式更容易阅读，我使用了`x`选项。该选项允许正则表达式写在新行上，并且空白也被忽略。*

最后，让我们用常量`COLORS_REGEXP`替换正则表达式`$colors_regexp`。

```
my  @colors = qw(blue yellow orange red);

say $_ =~ COLORS_REGEXP
    ? "$_ is valid"
    : "$_ is invalid" foreach @colors; 
```

Enter fullscreen mode Exit fullscreen mode

以上输出:

```
blue is valid
yellow is invalid
orange is invalid
red is valid 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Perl 5 中使用常量的字符串插值与标量和列表的工作方式相同，除了使用反斜杠`\`来引用它们。然而在 Perl 6 中，你可以只写`say "${ constant_name }"`就可以了！Perl 还有 [sprintf](https://perldoc.perl.org/functions/sprintf.html) 函数，它允许格式化嵌入在字符串中的变量。

感谢您的阅读，本文使用的代码可在 [Github](https://gist.github.com/kirklewis/f2b9987cb330e7b702d1e8ed46f86a22#file-perl5-string-interpolation-pl) 上获得。