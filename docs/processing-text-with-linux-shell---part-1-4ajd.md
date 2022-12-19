# 用 Linux Shell 处理文本——第 1 部分

> 原文：<https://dev.to/shamil/processing-text-with-linux-shell---part-1-4ajd>

# 进入`sed`的世界

如果你每天都在使用任何* *nix* 系统，很可能你已经熟悉了，或者至少你听说过`sed`命令。

`sed`，是`Stream Editor`的缩写，是一个文本转换工具，捆绑在每个 unix 系统中。使`sed`区别于其他文本编辑器的是文本处理的速度。`sed`只对输入文本进行一次处理，因此处理速度更快。

### #替换那些难看的文字

是一个非常强大的工具，可以用一段文字替换另一段文字。可以使用正则表达式来匹配文本。

```
sed 's/text_to_be_replaced/replacement_text/' file_name 
```

然而，这只会打印控制台中的替换文本，而不会改变文件本身中的相同内容。如果我们想保存对文件的修改，我们可以使用`-i`标志。

```
sed -i 's/text_to_be_replaced/replacement_text/' file_name 
```

以上仅替换每行中第一次出现的给定模式。因此，如果我们想要替换模式的每一次出现，我们可以在末尾附加`g`参数。

```
sed 's/text_to_be_replaced/replacement_text/g' file_name 
```

注意，我们在上述命令中使用的分隔符`/`不是固定的，我们可以在`sed`中使用几乎任何分隔符。例如，

```
sed 's:text_to_be_replaced:replacement_text:g' file_name

sed 's|text_to_be_replaced|replacement_text|g' file_name 
```

好吧，但是如果定界符本身就是要被替换的模式的一部分呢？ⓧ_ⓧﮌ

我们可以用反斜杠来转义这个字符。比如用`below -`代替`following:`这个词，我们可以这么做:

```
sed 's:following\::below - :' file_name 
```

注意在分隔符`:`之前使用了`\:`来分隔模式和它的替换。

### #删除该报废

`sed`也允许我们从文件中删除行。`d`选项用于指示删除操作。删除行的通用语法是

```
sed 'Nd' file_name 
```

这里的`N`是我们想要删除的行号。如果我们想从文件中删除第 10 行，`N`应该是 10。

该命令最常见的用途是删除文件中的所有空行。

```
sed '/^$/d' file_name 
```

以上将删除文件中所有的空行。正则表达式`^$`标记一个空行，而`d`选项指定应该删除该行。

不是这个。我们还可以指定应该删除的行的范围。

```
sed 'm,nd' file_name 
```

上述命令将删除从第`m`行到第`n`行的所有行。

### #流水线很重要

现在，如何流水线化多个`sed`命令呢？

我们可以通过管道传输任意多的`sed`,它们将按顺序被处理。考虑下面的例子。

```
echo Linux | sed 's/L/l/' | sed 's/n/N/' | sed 's/l/L/' | sed 's/x/X/' 
```

这将输出`LiNuX`。

最后，让我们看看如何在`sed`命令中使用变量。到目前为止，我们在命令中使用了`' '`(单引号)。然而，当我们需要在命令中使用表达式时，我们也可以使用`" "`。看看下面的例子。

```
greet=hello

echo hello shamil | sed "s/$greet/hi" file_name 
```

这将替换评估`$greet`和的值，并用`hi`替换`hello`。

### #防患于未然

在`sed`命令中使用`-i`时，我们需要小心，因为它会替换文件中的实际内容。(相信我，我已经做过很多次了)

因此，最好首先使用不带`-i`标志的命令，并检查替换是否正确。但是，如果文件内容太长，无法像这样检查，您可以使用下面的命令创建一个相同的备份副本，然后修改内容。

```
sed -i.bak '12,30d' file_name 
```

这将删除从 12 到 30 的所有行，但最重要的是，在修改实际文件之前，它将在同一个目录*中创建一个`file_name.bak`。*

谁知道呢，这可能最终会保住你的工作(◠﹏◠)

(编辑:参见[此评论](https://dev.to/moopet/comment/4dna)了解更多关于`-i`用法的信息)