# PHP 中的双引号和单引号

> 原文：<https://dev.to/morinoko/double-quotes-vs-single-quotes-in-php-2e5n>

这是我不久前刚开始学习 PHP 时在旧博客上写的一篇文章。我会转贴在这里，以防对任何人有帮助:)

如果你想将文本保存为一个变量或者显示在屏幕上(比如说，`echo`或者`print`)，你必须用引号将文本括起来。用引号将文本括起来会使该文本成为字符串。

您可以使用单引号(`' '`)或双引号(`" "`)，但有一些重要的区别，我将在本文中讨论。

## 单引号

单引号是构成字符串最简单的方法。它们只是显示给它们的东西，没有花里胡哨的东西，没有特殊的“能力”，比如能够显示变量值(见下面的双引号部分)。

```
// Using single quotes to save a string in a variable:
$recipe_title = 'Meatball Spaghetti';

// Using single quotes to write something on the screen:
echo '<h1>Meatball Spaghetti</h1>';

// The line above will get output as-is in your code:
<h1>Meatball Spaghetti</h1> 
```

Enter fullscreen mode Exit fullscreen mode

### 用单引号换行

如果需要在多行上显示文本，可以在引号内使用换行符来实现。例如:

```
print '<ul>
  <li>Flour - 300 grams</li>
  <li>Butter - 200 grams</li>
  <li>Water - 100 ml</li>
</ul>'; 
```

Enter fullscreen mode Exit fullscreen mode

这将输出为:

```
<ul>
  <li>Flour - 300 grams</li>
  <li>Butter - 200 grams</li>
  <li>Water - 100 ml</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

如果你试图在多行中使用多个`print`或`echo`，它不会以同样的方式工作。比如:

```
print '<ul>';
print '<li>Flour - 300 grams</li>';
print '<li>Butter - 200 grams</li>';
print '<li>Water - 100 ml</li>';
print '</ul>'; 
```

Enter fullscreen mode Exit fullscreen mode

这将输出为:

```
<ul><li>Flour - 300 grams</li><li>Butter - 200 grams</li><li>Water - 100 ml</li></ul> 
```

Enter fullscreen mode Exit fullscreen mode

### 特殊字符和转义字符用单引号括起来

对于单引号，你只有两种选择:

一:`\'`转义单引号字符串中的单引号

如果您想在字符串中包含字符`'`,您需要对其进行转义。比如:

```
echo 'Za\'atar is a Middle Eastern spice mix.'; 
```

Enter fullscreen mode Exit fullscreen mode

这将正确地输出以下内容，而不会导致错误。

`Za'atar is a Middle Eastern spice mix.`

二:`\\`转义字符串中的反斜杠——转义字符

你可以使用

```
echo 'A \\ is called a "backslash."'; 
```

Enter fullscreen mode Exit fullscreen mode

要打印

`A \ is called a "backslash."`

*这可能取决于编译器，但当我测试时，`\`似乎也能自己工作。但是，如果出于某种原因你想显示`\\`，你可能需要使用类似`echo '\\\';`或`echo '\\\\';`的东西。既然是特殊人物，为了以防万一，最好还是逃吧。

## 双引号

双引号和单引号的一个很大的区别是，你可以使用双引号直接在字符串中包含变量。如果你使用单引号，你将不得不把这些片段连接在一起。让我们看一个例子。

假设你有食谱，你将标题保存到一个名为`$recipe_title` :
的变量中

```
$recipe_title = 'Meatball Spaghetti'; 
```

Enter fullscreen mode Exit fullscreen mode

如果您想为食谱标题创建 HTML，使它们看起来像这样(并且您没有将 PHP 直接嵌入 HTML 文件，在 HTML 文件中您可能会使用`<?php ?>`标签来显示变量):

```
<h1>Meatball Spaghetti</h1> 
```

Enter fullscreen mode Exit fullscreen mode

使用单引号，您需要将不同的部分加在一起:

```
echo '<h1>' . $recipe_title . '</h1>'; 
```

Enter fullscreen mode Exit fullscreen mode

然而，使用双引号，您可以将变量直接放在引号内:

```
echo "<h1>$recipe_title</h1>"; 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都很好，但是使用双引号可以节省一些麻烦。

### 亲提示:

在将变量名解析成双引号字符串时，使用花括号显式指定变量名的结尾。

试图在下面的第 2 行打印 2 个杯子会给你一个错误，因为代码认为变量名是$unit_cups 而不是`$unit_cup` :

```
$unit_cup = "cup";
print "Flour - 2 $unit_cups"; 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这样的错误，您可以将变量名用大括号括起来，比如:

```
$unit_cup = "cup";
print "Flour - 2 ${unit_cup}s"; 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在双引号中进行一些更复杂的操作，但这超出了本文的范围。要了解更多关于解析双引号中复杂运算的信息，请查看 PHP 手册中的例子。

-

顺便说一下，像单引号一样，您可以通过在字符串中包含换行符来在输出中添加换行符。例如，

```
print "<ul>
  <li>Gin - 3 ounces</li>
  <li>Tonic - 4 ounces</li>
  <li>Lime - 1 slice</li>
</ul>"; 
```

Enter fullscreen mode Exit fullscreen mode

显示:

```
<ul>
  <li>Gin - 3 ounces</li>
  <li>Tonic - 4 ounces</li>
  <li>Lime - 1 slice</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

### 特殊字符和转义字符用双引号括起来

双引号比单引号提供了更多的特殊字符，包括换行符。

*   `\n`换一个新的行
*   `\t`对于标签页
*   `\r`回车
*   `\$`美元符号(否则可能被误认为变量)
*   `\"`为双引号
*   详见 [PHP 手册](http://php.net/manual/en/language.types.string.php#language.types.string.syntax.double)

## 该用什么？

一般来说，您可以使用或，但您应该保持一致，什么时候使用什么类型。例如，默认情况下，您可以选择使用单引号，除非您需要在字符串中使用变量或特殊字符。

您可能会认为，因为双引号给了您更多的功能，所以一直使用它们会更好，但单引号可能更适合简单的字符串，因为您不需要对美元符号等特殊字符进行转义。