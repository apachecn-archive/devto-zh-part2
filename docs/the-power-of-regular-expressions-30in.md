# 正则表达式的力量

> 原文：<https://dev.to/awwsmm/the-power-of-regular-expressions-30in>

正则表达式(或称 *regex* )是一个相对容易学习的概念，但它会对代码的可读性、可维护性和性能产生巨大的影响。所有主要的编程语言都支持正则表达式，但是 [Groovy，一种 Java 虚拟机(JVM)语言](https://blog.teamtreehouse.com/regular-expressions-10-languages)似乎提供了最优雅的实现，所以在本教程中我将使用 Groovy。请记住，生活中没有什么是简单的，因此有许多不同的正则表达式变体(或“风味”)，支持不同的特性。我会尽量坚持所有口味都有的东西，但我会记下什么时候不是这样。

# Groovy 中的基本正则表达式

正则表达式是定义模式的字符序列。可以在其他字符序列(或字符串)中搜索该模式。正则表达式可以简单到

```
groovy:000> stringToSearch = "A string that contains the letter 'z'."
===> A string that contains the letter 'z'.

groovy:000> thingToFind = stringToSearch =~ /z/
===> java.util.regex.Matcher[pattern=z region=0,38 lastmatch=]

groovy:000> thingToFind.find()
===> true

groovy:000> thingToFind.start()
===> 35

groovy:000> stringToSearch[35]
===> z 
```

Enter fullscreen mode Exit fullscreen mode

这里我使用的是 [Groovy Shell](http://www.groovy-lang.org/groovysh.html) ，当你使用 SDKMAN[安装 Groovy 时它是免费的！](http://groovy-lang.org/install.html#SDKMAN)。注意，我们要搜索的字符串只是一个典型的字符串，用引号(`"`)括起来。regex 模式也是一个字符串，但是它被正斜杠(`/`)包围。这就是所谓的[“斜线串”](http://groovy-lang.org/syntax.html#_slashy_string)。在斜杠字符串中，唯一需要转义的特殊字符是正斜杠本身，它用反斜杠(`\/`)进行转义。这是 Groovy 特有的模式。大多数其他语言(如 Java)要求您对 regex 字符串中的所有特殊字符进行转义，并且没有用于正则表达式的特殊斜杠字符串。

Groovy 使用 [`java.util.regex.Matcher`](https://docs.oracle.com/javase/10/docs/api/java/util/regex/Matcher.html) 作为它的正则表达式引擎，因为它是一种基于 JVM 的语言，构建在 Java 之上。但是 Groovy 还提供了一个特殊的操作符，称为 [*find 操作符*](http://docs.groovy-lang.org/docs/next/html/documentation/core-operators.html#_pattern_operator) ，`=~`，它定义了要匹配的模式，并将其与提供的字符串进行匹配。

[`java.lang.String.indexOf`](https://docs.oracle.com/javase/10/docs/api/java/lang/String.html#indexOf(java.lang.String)) :
支持匹配字符串内的子字符串

```
groovy:000> substring = "in"
===> in

groovy:000> stringToSearch.indexOf(substring)
===> 5

groovy:000> stringToSearch[5..6]
===> in 
```

Enter fullscreen mode Exit fullscreen mode

Groovy 支持使用下标操作符`[a..b]`的[数组切片](http://docs.groovy-lang.org/next/html/documentation/working-with-collections.html#Collections-Slicingwiththesubscriptoperator)(返回数组元素的选定范围)。如果有多个匹配，我们可以在第一个匹配之后开始第二个搜索，以此类推，寻找更多的匹配:

```
groovy:000> stringToSearch.indexOf(substring, 6)
===> 19

groovy:000> stringToSearch[19..20]
===> in 
```

Enter fullscreen mode Exit fullscreen mode

这个功能当然可以使用正则表达式来复制:

```
groovy:000> anotherThing = stringToSearch =~ /in/
===> java.util.regex.Matcher[pattern=in region=0,38 lastmatch=]

groovy:000> anotherThing.find()
===> true

groovy:000> anotherThing.start()
===> 5

groovy:000> anotherThing.find()
===> true

groovy:000> anotherThing.start()
===> 19

groovy:000> anotherThing.find()
===> false

groovy:000> anotherThing.start()
ERROR java.lang.IllegalStateException:
No match available
        at java_util_regex_MatchResult$start$1.call (Unknown Source) 
```

Enter fullscreen mode Exit fullscreen mode

如果可以找到一个额外的(或第一个)匹配，则`find()`返回`true`，然后`start()`返回该匹配的索引。如果`find()`找不到匹配，它返回`false`，然后试图运行`start()`将返回一个错误。

# 字符和元字符

如果您只想在给定的一段文本中查找特定的字符或子字符串，上面的例子就很好。但是如果要匹配更复杂的东西呢？电话号码？地址？匹配 URL 或验证电子邮件地址呢？为了描述比简单的文字字符串更复杂的东西，我们需要引入所谓的*元字符*。

在整个教程中，我建议你在 regex101.com T2 尝试这些正则表达式。

## 基本元字符

元字符——顾名思义——除了字面意思之外，还有其他含义的字符。元字符的含义取决于它的上下文。您以前肯定遇到过元字符，可能没有意识到。前一句末尾的句点(或“点”或“句号”)是一种元字符。在这种情况下，句号表示特定句子的结尾，但也可以用作浮点数中的小数点，或者电话号码(800.555.1234)和网址(timecube.2enp.com)中的分隔符。

正则表达式使用元字符来对字符进行分组，允许不同的字符组，等等。句点是正则表达式中的元字符，匹配任何单个字符(注意:一些正则表达式引擎不把换行符算作“字符”):

```
. == any single character 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果你想写一个匹配单词“cat”和“cut”的正则表达式，你可以写:

```
c.t 
```

Enter fullscreen mode Exit fullscreen mode

...但是`.`匹配任何字符，所以这个表达式也匹配“cot”或“c@t”或“c？" t "或" c t "或其他什么。要限制匹配，您可以尝试使用括号表达式:

```
[abc] == matches a OR b OR c 
```

Enter fullscreen mode Exit fullscreen mode

方括号是元字符。方括号内的任何字符都按字面解释(因此`.`被解释为字面上的句号字符，而不是“任何单个字符”)。括号表达式允许匹配比句号更具体的情况。例如，我们可以将之前的正则表达式调整为只有匹配单词“猫”和“切”，如下:

```
c[au]t 
```

Enter fullscreen mode Exit fullscreen mode

`[au]`将匹配单个‘a’字符或单个‘u’字符，但不能两者都匹配(如“caut”、“caat”、“cuut”等)。不匹配)。上面的正则表达式匹配单词“cat”和“cut”并且只匹配单词“cat”和“cut”。如果我们想排除结果呢？比方说，当单词是“猫”或“切”时，我们希望匹配除了之外的`c.t` *。嗯，我们可以在括号表达式中使用克拉元字符:* 

```
c[^au]t == matches any string c.t except "cat" and "cut" 
```

Enter fullscreen mode Exit fullscreen mode

以上匹配以“c”开头、以“t”结尾的任何三个字符的字符串，前提是中间字符是*而不是*“a”或“u”。克拉元字符有双重作用:当它出现在方括号表达式之外时，它的意思是“字符串的开头”，对于基于行的应用程序，它的意思是“行的开头”:

```
^[ ] == matches a single space at the beginning of a string / line 
```

Enter fullscreen mode Exit fullscreen mode

因此，这将匹配字符串中的前导空格`" hello"`，但不会匹配`"hello world"`，因为，即使该字符串中有空格，它也不会出现在字符串的开头。一个类似的字符用于匹配一个字符串或一行的*结尾*，美元符号:

```
[.!?]$ == matches a period, exclamation point, or question mark at the end of a string / line 
```

Enter fullscreen mode Exit fullscreen mode

括号表达式中的美元符号按字面意思解释为美元符号字符。当它在括号表达式之外时，它被解释为“字符串/行的结尾”。回过头来看克拉，它是一个元字符，包括括号内和括号外的表达式，不是吗？那么，我们如何准确地匹配一克拉呢？我们可以使用反斜杠
对字符进行转义

```
\^ == matches the carat character literally 
```

Enter fullscreen mode Exit fullscreen mode

...或者，更简单地说，就是不把它放在括号表达式的左边:

```
[a^b] == matches any of the characters 'a', '^', or 'b' 
```

Enter fullscreen mode Exit fullscreen mode

反斜杠用于定义*转义序列*，它可以包括外国字母表、表情符号等字符的 unicode 表示，但通常用于不可打印的字符，如制表符和换行符，以及转义特殊字符，如克拉。一些常见的转义序列包括:

```
\n == matches a newline / line break / line feed
\r == matches a carriage return
\t == matches a tab character 
```

Enter fullscreen mode Exit fullscreen mode

请记住，DOS 使用回车和换行符(`\r\n`)作为“行尾”标记，而 Unix 只使用换行符(`\n`)，这就是为什么从 Mac 传输到 Windows 的文本文档有时会丢失所有换行符。大多数情况下，大多数字符都是按字面解释的，但是您可以通过在任何*元字符前面加一个反斜杠来强制对其进行字面解释。例如:* 

```
\. == matches a literal full stop character
\\ == matches a literal backslash character
\[ == matches a literal opening bracket character
\] == matches a literal closing bracket character 
```

Enter fullscreen mode Exit fullscreen mode

...诸如此类。注意，这不适用于字母字符(例如，我们在上面看到`\t`匹配一个制表符，而不是文字‘t’字符)。一般来说，字母数字字符按字面解释，而大多数其他字符有一些元解释。但是当包含在括号表达式中时，这些字符可以按字面意思解释:

```
[.^$] == matches any of the characters '.', '^', or '$' 
```

Enter fullscreen mode Exit fullscreen mode

此规则的唯一例外是，当括号表达式中的第一个字符是克拉字符时，如前所述，反斜杠字符和括号字符本身必须进行转义:

```
[\[\]\\] == matches any of the characters '[', ']', or '\' 
```

Enter fullscreen mode Exit fullscreen mode

最后一个基本元字符是“交替”字符(又名。“choice”或“set union”)，这与 Unix 中的“pipe”字符相同，`|`。choice 字符允许您提供两个可选匹配，竖线的每一边一个。因此，如果你想匹配某人的名字*或*他们的昵称，你可以这样做:

```
Robert|Bob == matches "Robert" or "Bob", but not both 
```

Enter fullscreen mode Exit fullscreen mode

对于更复杂的匹配，您可以组合选项和括号:

```
[BR]ob|Ric[hk] 
```

Enter fullscreen mode Exit fullscreen mode

以上匹配“Bob”、“Rob”、“Rich”或“Rick”中的任何一个名称。

## 量词

使用点号`.`、括号`[]`、克拉符号`^`、交替符号`|`和美元符号`$`，我们可以匹配特定的字符，提供可接受和不可接受的字符组、备选匹配，并指定这些匹配应该出现在字符串/行的开头或结尾。有了反斜杠`\`，我们就有能力通过转义序列来指定任何应该被认为是我们匹配的一部分的字符。但是如果我们想看一个角色一定次数呢？现在，我们没有办法指定，但是 *Kleene star* (也称为“the star”或“glob”)允许我们匹配零个或多个前面的字符，就像这样:

```
lol* == matches "lo", "lol", "loll", "lolll", etc. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用上面的正则表达式来看看有人认为我们有多有趣，正如我们所知，这与“lol”结尾的“l”的数量直接相关。但是上面也符合“lo”(让我们听起来像是要发布一些不好的、过时的消息)，因为结尾的`l*`意味着零尾随“l”是可以接受的。为了解决这个问题(并指定我们希望在末尾至少有一个“l”)，我们可以写:

```
loll* == matches "lol", "loll", "lolll", etc. 
```

Enter fullscreen mode Exit fullscreen mode

POSIX Extended regex 规范为此提供了一个快捷方式，`+`元字符，意思是“一个或多个”前面的字符。所以上面相当于:

```
lol+ == matches "lol", "loll", "lolll", etc. 
```

Enter fullscreen mode Exit fullscreen mode

如果我们认为人们对他们的 lol 不真诚，我们只想接受标准的“lol”和稍微更热情的“lol”，我们可能只想要一两个 l 结尾。POSIX Extended regex 提供了另一种速记法，它只接受前面字符的零个或一个副本，即`?`元字符:

```
loll? = matches only "lol" or "loll" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想剔除那些奉承者，只找到那些在 lol 结尾用了两个以上的 l 的人呢？嗯，我们可以再次使用`+`元字符，编写类似于
的代码

```
lolll+ == matches "lolll", "lollll", "lolllll", etc. 
```

Enter fullscreen mode Exit fullscreen mode

...或者，我们可以使用另一个基本元字符(我们还没有引入)，花括号元字符(或“括号”)，`{}`。大括号可以用来精确地指定*前面的字符出现了多少次(当像`{a}`一样使用时，其中`a`是某个非负整数)；可接受事件的最小数量(当像`{a,}`一样使用时)；或者可接受的最小和最大出现次数(当像`{a,b}`一样使用时，其中`a`和`b`都是非负整数，而`b > a`)。上面的正则表达式可以重写为:* 

```
lol{3,} == matches "lolll", "lollll", "lolllll", etc. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以进一步分割这些匹配，以多种方式使用括号:

```
lol{3}   == matches "lolll" only
lol{4,6} == matches "lollll", "lolllll", or "lollllll"
lol{7,}  == matches "lolllllll", "lollllllll", and so on 
```

Enter fullscreen mode Exit fullscreen mode

坦率地说，这是一个令人不安的数字。让我们通过学习懒惰和占有欲匹配，把这些从我们的生活中剔除。

### 懒惰 vs 贪婪 vs 占有欲匹配

当我们在几段前了解到`+`元字符时，我说过它匹配前面字符的“一个或多个”出现。但是这个正则表达式如何“决定”匹配多少次呢？例如，正则表达式"`loll+`"匹配字符串"`lolllll`"匹配"`loll`"或"`lolll`"或"`lollll`"或什么？

在 Python 和 Java 以及其他一些 regex 引擎中，量词`+`、`*`和`?`默认为*贪婪*。他们捕获尽可能多的字符，只要这不会导致匹配失败。那么，上述问题的答案是，“`loll+`”将匹配整个字符串“`lolllll`”。限定词“只要不导致匹配失败”，最好用一个例子来说明。考虑字符串:

```
while (a < b) { while (c < d) { --d;}; while (b < c) { ++a; --c; } } 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在编写自己的语法高亮器或编译器，您可能需要像这样解析一行。大多数现代编辑器都提供了“括号匹配”，当用户将光标放在左括号`{`上时，相应的右括号`}`会高亮显示。如果你想要一个简单的正则表达式来查找开始括号，中间的所有内容，然后是结束括号，你可以写如下:

```
{.*} 
```

Enter fullscreen mode Exit fullscreen mode

这应该匹配一个开括号`{`，后面跟着任意字符`.`任意次`*`，后面跟着一个闭括号，对吗？默认情况下，是的。这是上面的正则表达式在那一行代码中匹配的内容:

```
 { while (c < d) { --d;}; while (b < c) { ++a; --c; } }
              {....................................................} 
```

Enter fullscreen mode Exit fullscreen mode

(添加了前导空格，以便与原始行进行直观比较。)我已经通过在完全匹配下面添加一行“legend”来显示哪些字符与`.`匹配。`.*`捕获尽可能多的字符，只要它不会导致匹配失败。我们可以通过在`*`后面添加一个`+`来使其成为所有格，就像这样:

```
{.*+} 
```

Enter fullscreen mode Exit fullscreen mode

`.*+`现在将捕获尽可能多的字符，即使它导致匹配失败，对于我们的例子来说，它确实失败了:

```
 { while (c < d) { --d;}; while (b < c) { ++a; --c; } }
              {..................................................... 
```

Enter fullscreen mode Exit fullscreen mode

现在，`.*+`甚至捕获了最后一个`}`字符，正则表达式不匹配，因为`.*+`已经“吃掉”了它需要结束的`}`。那就是所有格匹配。相反，*惰性匹配*可以通过在量词后面追加`?`而不是`+`来实施。因此，对于上述内容，如果我们重写我们的正则表达式如下:

```
{.*?} 
```

Enter fullscreen mode Exit fullscreen mode

`.*?`现在将捕获尽可能少的字符，前提是不会导致匹配失败:

```
 { while (c < d) { --d;}
              {.....................} 
```

Enter fullscreen mode Exit fullscreen mode

正则表达式匹配它看到的第一个右括号，即使它后面有更多的字符。这就是懒搭配。在这种情况下，延迟匹配并不是非常有用，因为代码块可以放在其他代码块中，但是对于解析可能包含引用表达式的文本这样的事情来说，延迟匹配非常有用。引号不能存在于其他引号中，所以懒惰匹配(在我们用第一个`"`“打开”一个引号后寻找下一个`"`)是可行的方法。注意，这也适用于像 XML 和 HTML 注释这样不能嵌套的东西。

## 字符范围和类别

提供大量的备选字符可能会很乏味。假设我们想要匹配一个美国式的邮政编码，它只是五个连续的数字。为了用我们到目前为止所写的内容做到这一点，我们可以写:

```
[0123456789][0123456789][0123456789][0123456789][0123456789] 
```

Enter fullscreen mode Exit fullscreen mode

...或者，更简洁地说

```
[0123456789]{5} == matches any 5-digit number from 00000 to 99999 
```

Enter fullscreen mode Exit fullscreen mode

但是用 regex 有一种更简洁的方法，使用*范围*。使用`-`字符在括号表达式中指定范围。例如，我们可以只写
，而不是`[0123456789]{5}`

```
[0-9]{5} == matches any 5-digit number from 00000 to 99999 
```

Enter fullscreen mode Exit fullscreen mode

上面的正则表达式完成了与前面的正则表达式相同的事情，但是更加简洁。当范围与字母字符一起使用时，可以节省更多的空间:

```
[A-Z] == matches any uppercase letter 'A' through 'Z' ("upper")
[a-z] == matches any lowercase letter 'a' through 'z' ("lower") 
```

Enter fullscreen mode Exit fullscreen mode

范围可以被组合和修剪，并且用括号内的非范围字符连接，因此我们可以定义如下内容:

```
[A-Za-z0-9]   == matches any alphanumeric ("alnum") character ("alpha" + "digit")
[A-Za-z0-9_]  == matches any "word" character ("alnum" + '_')
[A-Fa-f0-9]   == matches any hexadecimal digit ("xdigit") 
```

Enter fullscreen mode Exit fullscreen mode

...诸如此类。其他常见的字符类别包括:

```
[A-Za-z]      == matches any alphabetic ("alpha") character ("upper" + "lower")
[0-9]         == matches any numeric character ("digit")
[ \t\r\n\v\f] == matches any whitespace character ("space") 
```

Enter fullscreen mode Exit fullscreen mode

这些集合中的任何一个都可以用`^`来否定。例如，我们可以用
得到所有非字母数字的字符(“alnum”)

```
[^A-Za-z0-9]  == matches any non-alphanumeric character 
```

Enter fullscreen mode Exit fullscreen mode

许多(但不是所有)正则表达式引擎都有这些字符集的快捷方式，但这些快捷方式有时会因引擎而异。例如，一些“alpha”快捷键包括:

```
[:alpha:]  ==  POSIX "alpha" alias
\a         ==  Perl/Tcl "alpha" alias
\p{Alpha}  ==  Java "alpha" alias 
```

Enter fullscreen mode Exit fullscreen mode

最好不要假设您可以猜出这些别名，除非您计划在整个职业生涯中坚持使用一个正则表达式引擎。编写`[a-zA-Z]`是跨引擎兼容的，而且实际上比`[:alpha:]`或`\p{Alpha}`输入的字符更少——有时坚持基本原则会更好。(如果你想快速浏览一些常见的角色类别，你可以在这里查看[。)](https://en.wikipedia.org/wiki/Regular_expression#Character_classes)

## 捕捉群体

在基本正则表达式的概述中，最后要介绍的是*捕获组*(也称为“子表达式”或只是“组”)的概念。子表达式由一组括号`()`定义，可用于将字符组合在一起或提供多个“匹配”。例如，我们可能想要匹配 HTML 的开始和结束标记，以确保开始标记中的内容与结束标记中的内容相匹配:

```
<(.+)>.*?</(.+)> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 Groovy 中使用带引号的 regex 字符串，而不是正斜杠字符串(在 regex101 和 Groovy slashy 字符串中，您需要在结束标记前加一个反斜杠来转义正斜杠):

```
groovy:000> s = "<b>thing</a>"
===> <b>thing</a>

groovy:000> m = s =~ "<(.+)>.*?</(.+)>"
===> java.util.regex.Matcher[pattern=<(.+)>.*?</(.+)> region=0,12 lastmatch=]

groovy:000> m.find()
===> true

groovy:000> m.group(0)
===> <b>thing</a>

groovy:000> m.group(1)
===> b

groovy:000> m.group(2)
===> a 
```

Enter fullscreen mode Exit fullscreen mode

第`0`组是整体表达式匹配，而后续组是子表达式匹配。我们的正则表达式有两个已定义的子表达式，所以它们被编号为组`1`和`2`。我们可以在 HTML 标签上运行这个正则表达式，以确保开始标签与结束标签匹配。

或者，我们可能希望通过组合捕获组、选择和量词来提供多个备选匹配:

```
(\+1 )?(([(][0-9]{3}[)] )|([0-9]{3}[ .-]))[0-9]{3}[ .-][0-9]{4} 
```

Enter fullscreen mode Exit fullscreen mode

上述正则表达式将匹配任何正常格式的美国电话号码。例如:

```
+1 555-234-1234   == matches
(654) 999-0234    == matches
+1 (101) 234 9838 == matches
333 444.5555      == matches 
```

Enter fullscreen mode Exit fullscreen mode

...这个正则表达式允许三组数字之间的分隔符不同，例如:

```
111.234-3463 == matches 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点不寻常，可以用更复杂的验证方案过滤掉。

如果需要再次重复相同的组，可以在正则表达式中引用捕获组。这样可以节省打字，减少错误。要在正则表达式中引用第`N`个组，只需使用快捷键`\N` :

```
(shoo)(bee)(doo)(\2) 
```

Enter fullscreen mode Exit fullscreen mode

上面的正则表达式将匹配字符串“shoobeedoobee”。以下正则表达式将匹配字符串“doowopdoowop”，但不匹配“doowop”:

```
(doo)(wop)\1\2 
```

Enter fullscreen mode Exit fullscreen mode

像这样最多只能引用九个捕获组(`\1`到`\9`)，因为斜杠前面的两三位数被解释为八进制数字或字符索引(`\103`将被解释为字符`C`)。

# 例子

这里有一些示例正则表达式，可能会对您有所启发！如果您知道任何有用的模式，请在下面提交您自己的模式！

## 电子邮件地址

我现在住在爱尔兰，但我经常回美国看望家人和朋友。由于我不够开明，没有双卡手机，我不得不求助于在机场和星巴克注册免费 WiFi。许多这样的登录页面使用简单的正则表达式来检查电子邮件地址，这样他们就可以向你发送垃圾邮件，或者出售你的信息来赚更多的钱。用于验证电子邮件地址的一个非常简单的正则表达式可能是这样的:

```
.+@.+\..++ 
```

Enter fullscreen mode Exit fullscreen mode

这将捕获大多数电子邮件地址，包括以`.co.uk`结尾的，但它也允许像`a@b.c`或`bob@bob.bob`(我最喜欢的两种一次性电子邮件)这样的垃圾邮件通过。如果你真的想验证一个电子邮件地址，你应该发送一封验证邮件到那个地址，并要求用户点击一个链接或什么的。

## 通用数字模式

不同的编程语言允许不同种类的数字表示。有些语言允许您在数字后面加上“F”或“F”来表示它应该被解释为浮点数(而不是“double”精度整数)，或者加上“L”或“L”来表示它应该是“long”(双宽度)整数。其他语言允许您使用“E”或“E”来表示科学记数法，允许前导“+”符号，等等。下面的正则表达式允许大多数不同种类的数字表示(没有尾随的‘f’和‘L’):

```
[+-]?([0-9]+\.?[0-9]*|\.[0-9]+)([eE][+-]?[0-9]+)? 
```

Enter fullscreen mode Exit fullscreen mode

## 解析代码

下面(极其复杂)的正则表达式解析 Java 方法的方法签名。它匹配任何有效的 Java 方法签名(据我所知！):

```
(?:(?:(public|protected|private)\s+)|(?:(abstract|static)\s+)|(?:(final)\s+)|(?:(volatile|synchronized)\s+)|(?:(native|strictfp)\s+))*([a-zA-Z_][[:alnum:]]+)\s+([a-zA-Z_][[:word:]<>\[\]]+)\s*\(\s*(?:(?:([a-zA-Z_][[:word:]<>\[\]]+)\s+([a-zA-Z_][[:alnum:]]+)\s*)(?:,\s*([a-zA-Z_][[:word:]<>\[\]]+)\s+([a-zA-Z_][[:alnum:]]+)\s*)*)?\)\s*\{ 
```

Enter fullscreen mode Exit fullscreen mode*