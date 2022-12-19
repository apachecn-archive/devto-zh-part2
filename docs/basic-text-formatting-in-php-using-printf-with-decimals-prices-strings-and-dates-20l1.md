# PHP 中使用 printf()的基本文本格式，包括小数、价格、字符串和日期

> 原文：<https://dev.to/morinoko/basic-text-formatting-in-php-using-printf-with-decimals-prices-strings-and-dates-20l1>

这是我不久前刚开始学习 PHP 时在旧博客上写的一篇文章。我会转贴在这里，以防对任何人有帮助:)

`printf()`功能让你以特定的格式打印一些东西。例如，您可以格式化价格，以确保它们总是只显示两位小数，而不是像`$4.5`或`$4.539999`这样的东西，或者您可以创建一个模板字符串，允许您坚持不同的变量，但每次需要显示它时保持相同的格式(例如，创建一个在菜单上显示行的模板)。

我试了几次才得到它。起初，我认为它涉及到处理太多的神秘符号和编码字母，但在你知道一些基本的之后，我看到了它是如何有用的。

所以，基本格式是

```
printf(<format template for string>, <variable to plug in>, <another variable to plug in>, ...) 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数，字符串的格式模板，就像一个普通的字符串，但是它使用了特殊的代码，这些代码被插件变量所替代。第二个参数和 on 是您要插入的变量。您可以拥有任意多个，但是它们应该按照在格式模板字符串中出现的顺序排列。

例如，`printf()`最常见的用途之一就是格式化价格:

```
$tofu = 1.30;
printf("\$%.2f", $tofu * 2);
// Output
$2.60 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那么那个`"\$%.2f"`是怎么回事？

会在你的价格前加一个美元符号。至于`%.2f`，这就是格式化你的价格。`%`表示格式化变量在这里，f 表示这是一个浮点数(带小数点的数)。对于浮点，您可以使用`.2`来指定您想要多少个小数点，在这种情况下，它确保数字有 2 个小数位。

在上面的例子中，如果你只是将豆腐的价格乘以 2，那么数字最终将是 2.6，所以我使用该格式来确保在这种情况下会添加一个零。

这里还有一个稍微复杂一点的例子。为了保持整洁，避免输入那些烦人的格式字符串，你可以将格式字符串保存到它自己的变量中，然后插入到`printf()`函数中，就像下面的`$price_format`变量:

```
$eggs = 1.99;
$tofu = 1.29;
$beer = 8.99;
$price_format = "\$%.2f\n";

print "Receipt\n";
printf("Eggs $price_format", $eggs * 2);
printf("Tofu $price_format", $tofu);
printf("Beer $price_format", $beer * 2);
printf("Total $price_format", ($eggs * 2) + $tofu + ($beer * 2));

// Output:
Receipt
Eggs $3.98
Tofu $1.29
Beer $17.98
Total $23.25 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用`%s`将字符串形式的变量添加到您的格式中。例如，如果我们还想将食品杂货的名称插入到格式字符串中:

```
$eggs = 1.99;
$tofu = 1.29;
$beer = 8.99;
$price_format = "%s \$%.2f\n";

print "Receipt\n";
printf($price_format, 'Eggs', $eggs * 2);
printf($price_format, 'Tofu', $tofu * 1);
printf($price_format, 'Beer', $beer * 2);
printf($price_format, 'Total', ($eggs * 2) + $tofu + ($beer * 2));

// Output
Receipt
Eggs $3.98
Tofu $1.29
Beer $17.98
Total $23.25 
```

Enter fullscreen mode Exit fullscreen mode

另一个使用`%s`插入字符串变量的例子，比如在一个网站的标题栏中(虽然，可能有更好的方法来做这件事):

```
$site_title = "Recipe Box";
$page_title = "Grandma's Apple Pie";
$title_format = "%s | %s";

printf($title_format, $site_title, $page_title);

// Output
Recipe Box | Grandma's Apple Pie 
```

Enter fullscreen mode Exit fullscreen mode

因为您可以指示特定变量的长度，并在需要时包含前导字符，所以`printf()`对于格式化日期也很方便:

```
$month = 1; $day = 24; $year = 2017; 
$date = sprintf("%02d/%02d/%04d", $month, $day,$year);

echo $date;

// Output
01/24/2017 
```

Enter fullscreen mode Exit fullscreen mode

在这个格式字符串`"%04d/%02d/%02d"`中，d 用来表示变量类型是整数(也有其他类型，但这是基本的一种)。`4s`和`2s`告诉函数这个数字是 4 位数或 2 位数长，而 0 是“填充字符”,会加在不够长的数字前面。因此，对于本例中的一月(`1`)，在打印的字符串`2017/01/24`中，它前面会有一个 0。

您可以做的一些其他事情是格式化数字以包含正负符号(例如温度)，轻松输出科学符号，等等。在 [PHP 手册 sprintf 页面](http://php.net/manual/en/function.sprintf.php)上查看更多格式选项。