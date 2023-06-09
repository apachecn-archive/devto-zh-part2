# 用 Vim 替换查找、转换和替换日期

> 原文：<https://dev.to/jkreeftmeijer/find-convert-and-replace-dates-with-vim-substitutions-4lph>

*最初发表于 2017 年 10 月 17 日[jeffkreeftmeijer.com/vim-reformat-dates](https://jeffkreeftmeijer.com/vim-reformat-dates/)。*

Vim 的 substitution 命令是修改文本文件的一种强有力的方法。除了使用正则表达式查找和替换文本，替换还可以调用外部程序进行更复杂的替换。通过使用替换中的`date`实用程序，Vim 可以将一个文件中的所有日期转换成不同的格式，并一次性替换它们。

[![Finding, converting and replacing dates with Vim<br>
substitutions](img/85f5ba6697c2d02f0671964b1ccc2fd1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JMRHBeik--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jeffkreeftmeijer.com/vim-reformat-dates/substitute.png)

输入文件是一个包含文章列表的 HTML 页面。每篇文章都包含一个`<time>`标签，该标签带有一个值和一个 datetime 属性来显示发布日期。

*输入文件*

```
<article>
  <h1>Keeping open source projects maintainable</h1>
  <time datetime="2017-09-19">2017-09-19</time>
</article>

<article>
  <h1>Property-based testing in Elixir using PropEr</h1>
  <time datetime="2017-08-22">2017-08-22</time>
</article>

<article>
  <h1>git is not a git command</h1>
  <time datetime="2015-10-01">2015-10-01</time>
</article>

... 
```

Enter fullscreen mode Exit fullscreen mode

我们需要将日期值转换为更友好的格式，包括完整的月份名称(“2017 年 9 月 19 日”)，同时保持日期时间属性的当前格式。

*结果:重新格式化日期的文章*

```
<article>
  <h1>Keeping open source projects maintainable</h1>
  <time datetime="2017-09-19">September 19, 2017</time>
</article>

<article>
  <h1>Property-based testing in Elixir using PropEr</h1>
  <time datetime="2017-08-22">August 22, 2017</time>
</article>

<article>
  <h1>git is not a git command</h1>
  <time datetime="2015-10-01">October 01, 2015</time>
</article>

... 
```

Enter fullscreen mode Exit fullscreen mode

输入文件有 40 多篇文章，所以手动替换它们是一项容易出错的工作。相反，我们编写了一个替代函数，在文件中查找所有日期，并用一个重新格式化的值替换它们。

## 寻找日期

替换日期的第一步是找到它们在输入文件中的位置，并确保不与 datetime 属性中的日期匹配。

为了找到文件中的所有日期，我们可以使用`....-..-..` ( `esc` `/....-..-..`)作为搜索模式来匹配日期格式。然而，该模式的结果将包括文件中所有匹配的日期，包括`<time>`标记的 datetime 属性中的日期。

```
<time datetime="2017-09-19">2017-09-19</time> 
```

Enter fullscreen mode Exit fullscreen mode

在输入文件中，所有的`<time>`值后面紧跟着来自结束`</time>`标记的小于号。为了防止属性中的日期时间包含在结果中，我们可以在搜索模式中包含小于号，并确保在替换时将其添加回来。

```
....-..-..< 
```

Enter fullscreen mode Exit fullscreen mode

然而，Vim 支持使用`\zs`和`\ze`模式原子在搜索模式中设置匹配的开始和结束。通过在我们的搜索模式中的`<`前面加上`\ze`，该模式会查找所有后面带有小于号的日期，但不会将其包含在匹配中，这意味着它不会被替换。

```
....-..-..\ze< 
```

Enter fullscreen mode Exit fullscreen mode

## 从命令行重新格式化日期

我们需要在日期替换中使用月份的全名，所以我们不能对输入值(“2017-09-19”)重新排序来获得我们想要的结果。相反，我们需要调用一个知道月份名称并可以在日期格式之间转换的外部实用程序。

我们使用`date`实用程序将搜索模式的每个匹配项重新格式化为我们想要的格式(“2017 年 9 月 19 日”)。我们使用`"%Y-%m-%d"`作为*输入格式*来匹配搜索模式的结果。*输出格式*是`"%B %d, %Y"`产生月份全称、日期数字、逗号和年份数字。

使用这些格式，`date`实用程序将`1991-11-02`重新格式化为`November 02, 1991`。

```
$ date -jf "%Y-%m-%d" "1991-11-02" +"%B %d, %Y"
November 02 1991 
```

Enter fullscreen mode Exit fullscreen mode

*   `-j`:不要试图设置系统日期
*   `-f "%Y-%m-%d"`:使用传递的输入格式，不使用默认格式。在这种情况下，`"%Y-%m-%d"`匹配输入格式(`1991-11-02`)
*   `"1991-11-02"`:使用传递给`-f`的输入格式解析的示例日期
*   `+"%B %d, %Y"`:输出格式，产生`November 02, 1991`

## 从替换中调用外部实用程序

我们知道如何找到文件中的所有日期，以及如何从命令行将日期转换为另一种格式。为了用来自`date`实用程序的重新格式化版本替换所有找到的日期，我们需要运行来自*替换*的*表达式*。

使用我们之前准备的搜索模式，我们可以找到输入文件中的所有日期值，并用一个替换来替换。例如，我们可以用硬编码值覆盖所有日期:

```
:%s/....-..-..\ze</November 2, 1991/gc 
```

Enter fullscreen mode Exit fullscreen mode

*   `....-..-..\ze<`:查找文件中所有日期的搜索模式
*   `November 2, 1991`:用硬编码的日期替换日期的文字替换字符串

我们不需要插入硬编码的替换字符串，而是需要为每个匹配运行一个表达式来获得它的替换。

我们使用表达式(`\=`)中的`system()`函数来调用`date`实用程序。现在坚持使用硬编码的日期，在将日期插入文件之前，我们可以使用该实用程序将日期格式从“1991-11-02”转换为“1991-11-2 ”:

```
:%s/....-..-..\ze</\=system('date -jf "%Y-%m-%d" "1991-11-02" +"%B %d, %Y"')/gc 
```

Enter fullscreen mode Exit fullscreen mode

*   `....-..-..\ze<`:查找文件中所有日期的搜索模式
*   `\=system('date …')`:使用`system()`函数执行外部命令并将其值作为替代字符串返回的表达式
*   `'date -jf "%Y-%m-%d" "1991-11-02" +"%B %d, %Y"'`:字符串形式的 date 命令，硬编码的日期(`"1991-11-02"`)作为其输入日期参数。该日期与搜索模式的匹配格式相匹配。

> ⚠️这种替换会在`<time>`标签中产生一个换行符，因为`date`实用程序会在其输出中附加一个换行符。我们将在后面讨论嵌套替换时删除这些内容。

替换值仍然是硬编码的(“1991-11-02”)，因此这种替换会将文件中的所有日期值改写为 1991 年的日期。要将匹配的日期值放回文件中，我们需要将它们传递给 date 命令。

为了将匹配的日期传递给表达式中对`date`的调用，我们需要中断传递给`system()`函数的字符串，并用对`submatch(0)`的调用替换硬编码的日期，以插入整个匹配。

```
:%s/....-..-..\ze</\=system('date -jf "%Y-%m-%d" "'.submatch(0).'" +"%B %d, %Y"')/gc 
```

Enter fullscreen mode Exit fullscreen mode

运行这个替换将把输入文件中的所有`<time>`标签转换成我们想要的格式，但是它用一个额外的换行符替换了结束的`</time>`标签。

当前结果，在结束的`</time>`标签
前添加了一个新行

```
<article>
  <h1>Keeping open source projects maintainable</h1>
  <time datetime="2017-09-19">September 19, 2017<
/time>
</article>

... 
```

Enter fullscreen mode Exit fullscreen mode

## 嵌套替换

一个新行被附加到`date`命令的结果中，在运行替换后，该结果在文件中结束。因为没有办法让 date 命令省略换行符，所以我们需要自己去掉它。

在运行第一个替换之后，我们可以运行第二个替换来删除它们:

```
:%s/\n<\/time>/<\/time>/g 
```

Enter fullscreen mode Exit fullscreen mode

为了防止最初的替换添加新行，我们
可以通过管道将`date`命令的结果传递给`tr`,用
和`-d`参数删除新行:

```
:%s/....-..-..\ze</\=system('date -jf "%Y-%m-%d" "'.submatch(0).'" +"%B %d, %Y" | tr -d "\n"')/gc 
```

Enter fullscreen mode Exit fullscreen mode

另一种选择是用嵌套替换去掉换行符。

### `substitute()`功能

Vim 的`substitute()`函数替换字符串，可以从替换表达式中运行。嵌套替换对于转换另一个函数的结果很有用。

函数(`substitute()`)的工作方式类似于替代命令(`:s`)，并且采用相同的参数，因此`substitute("input", "find", "replace", "g")`相当于在一个文件中运行`:%s/find/replace/g`。

`substitute()`函数的工作方式类似于 Vim 命令行中的替代命令(`:s[ubstitute]`),并采用相同的参数。第一个参数是输入，然后是搜索模式、替换字符串，后面是可选选项。`substitute("input", "find", "replace", "g")`相当于在一个文件中运行`:%s/find/replace/g`。

```
:echom substitute("October 02, 1991", "October", "November", "") 
```

Enter fullscreen mode Exit fullscreen mode

*   `"October 02, 1991"`:运行替换的输入字符串。
*   `"October"`:搜索模式。
*   `"November"`:替代字符串。
*   选项，就像“常规”替换一样。这个例子没有使用`g`选项，因为我们确信在输入字符串中只有一个匹配，所以这是不必要的。

如果从替换调用的外部命令返回一个尾随换行符(就像没有`-n`标志的`echo`一样)，我们可以使用`substitute()`函数在匹配被替换之前去掉它。

```
:%s/October/\=substitute(system('echo "November"'), "\n", "", "")/gc 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`substitute()`函数将对`date`实用程序的调用包装在嵌套替换中。它获取结果，匹配换行符(`"\n"`)并用一个空字符串替换它。

```
:%s/....-..-..\ze</\=substitute(system('date -jf "%Y-%m-%d" "'.submatch(0).'" +"%B %d, %Y"'), "\n", "", "")/gc 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的替换将把`<time>`标签转换成正确的格式，而不需要添加额外的换行符。

*结果*

```
<article>
  <h1>Keeping open source projects maintainable</h1>
  <time datetime="2017-09-19">September 19, 2017</time>
</article>

<article>
  <h1>Property-based testing in Elixir using PropEr</h1>
  <time datetime="2017-08-22">August 22, 2017</time>
</article>

<article>
  <h1>git is not a git command</h1>
  <time datetime="2015-10-01">October 01, 2015</time>
</article>

... 
```

Enter fullscreen mode Exit fullscreen mode