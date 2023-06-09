# Linux 提示和技巧

> 原文：<https://dev.to/mohanarpit/linux-tips-and-tricks-4kk>

似乎发表一篇 Linux 技巧的文章是所有工程师都必须经历的一个仪式。更重要的是，这是一个书签，让我未来的自己可以通过最少的谷歌搜索轻松找到这些折衷的命令组合。

所以事不宜迟，以下是我的清单:

### 找到

这个命令在大海捞针的时候非常有用。它也有大量的修饰语(太多了，无法列出)，所以我将只提到我几乎每天使用的那些。

该命令的一般语法是:

```
$ find <starting directory> \
  -type <type of file/directory> \
  -name "<Regex of the file/dir name>" \
  -exec <arbitrary command to execute> {} \; 
```

Enter fullscreen mode Exit fullscreen mode

查找 java 文件的基本示例:

```
$ find ~/projects -type f -name "*.java" 
```

Enter fullscreen mode Exit fullscreen mode

示例查找名为 main
的目录

```
$ find ~/projects -type d -name "main" 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以递归地对匹配的文件执行命令。这展示了 find 命令的真正威力。

示例:在所有 java 文件中搜索 Main 函数。这个命令的输出将只是匹配的字符串。

```
$ find ~/projects -type f -name "*.java" -exec grep -i "Main" '{}' \; 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您希望输出匹配文件的名称以及匹配的字符串:

```
$ find ~/projects -type f -name "*.java" -exec grep -i "Main" '{}' + 
```

Enter fullscreen mode Exit fullscreen mode

请注意命令结束方式的不同。运行时，大括号`'{}'`由每个匹配的文件名替换。和`xargs`的工作原理差不多。

### 少了

对于日志文件(通常很大)和我只希望读取并且
**不能**编辑的文件，我广泛使用`less`命令。我喜欢这个命令的主要原因之一是，我可以跟踪文件，而不会让终端变得混乱。这个命令还有一个好处，就是只有
将文件部分加载到内存中，从而使它成为`vi`的一个圆滑的
替代方案

示例:

```
$ less /var/log/insanely-large-log-file.log 
```

Enter fullscreen mode Exit fullscreen mode

一旦进入，您可以使用`G`转到文件的末尾，或者使用`F`结束文件。

或者，您可以使用
直接打开文件

```
$ less +F /var/log/insanely-large-log-file.log 
```

Enter fullscreen mode Exit fullscreen mode

要退出并返回终端，使用正常的`vi`命令

```
<ESC> :q 
```

Enter fullscreen mode Exit fullscreen mode

### 入口

我最近遇到了这个命令，虽然它没有成为我最常用的命令，但我认为它非常有用。编制这样一份清单而不提及`entr`将是一种犯罪

该命令允许用户在文件更改时执行任意脚本。这就类似于`watchr`、`guard`、&、`nodemon`。因为`entr`是用 C 写的，所以它在更大的目录下更快，响应更快。

用例的范围可以从每当你的源文件改变时运行测试用例

```
$ ls *.c | entr 'make && make test' 
```

Enter fullscreen mode Exit fullscreen mode

或者每当 HTML 文件改变时重新加载浏览器。

```
$ ls *.css *.html | entr reload-browser Firefox 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用`-r`修饰符
来重启服务器进程

```
$ ls *.rb | entr -r ruby main.rb 
```

Enter fullscreen mode Exit fullscreen mode

点击查看更多详情[。](http://www.entrproject.org/)

### htop

如果你除了
`htop`之外，以任何方式看待你的机器的性能，你就做错了。是`top`一直以来应该有的样子。虽然它不是内置的，但您可以通过:
轻松安装

```
$ apt install htop 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ brew install htop-osx. 
```

Enter fullscreen mode Exit fullscreen mode

输出非常简单明了，更容易理解和排序。

### jq

您对 JSON 解析器的搜索到此结束。运行在 JSON 数据流上。每个输入都被解析为一系列空格分隔的 JSON 值，这些值通过`jq`的每个过滤器。通过将一个过滤器的输出连接到另一个过滤器的输入，过滤器本身可以以任何方式组合。

示例:

这个例子从输入 JSON 中提取字段‘foo’。

```
$ jq '.foo' {"foo": 42, "bar": "less interesting data"} 
```

Enter fullscreen mode Exit fullscreen mode

```
=> 42 
```

Enter fullscreen mode Exit fullscreen mode

此示例提取 JSON 数组的第 0 个元素。

```
$ jq '.[0]' [{"name":"JSON", "good":true}, {"name":"XML", "good":false}] 
```

Enter fullscreen mode Exit fullscreen mode

```
=> {"name":"JSON", "good":true} 
```

Enter fullscreen mode Exit fullscreen mode

### vim 命令

普遍认为，维姆是镇上最牛逼的编辑。但对于新手来说，这可能有点令人生畏，也不友好。一旦你得到 vim，你将永远不会回到任何其他编辑器。

以下 vim 命令不适合新手。是给更高级的用户用的。

您多久在 vim 中打开一个文件进行编辑，并意识到您应该以 *root* 身份打开它？您可以使用下面的命令来保存您的更改，而无需退出 vim。

```
<ESC> :w !sudo tee % 
```

Enter fullscreen mode Exit fullscreen mode

打字时间稍长，但完全值得。

我认为 vim commands 需要一个专门的职位来公正地对待最受爱戴的编辑。

### 结论

我希望这是有用的。我可能会编辑这个帖子，以反映任何我遇到的/让我震惊的新命令。