# 带树贝壳盆景

> 原文：<https://dev.to/dmfay/shell-bonsai-with-tree-24h4>

shell 几乎拥有我日常操作计算机所需的所有工具:导航和管理目录和文件、文本编辑以及构建、测试和运行我正在处理的项目。它*不太擅长的是布局，或者说，显示任何不是文本文件的东西(尽管很有趣，但我不愿意为 [tiv](https://github.com/radare/tiv) 切换一个合适的图像浏览器)。*

目录树是最常见的布局之一，它不太适合等宽 ASCII 码。还有古老的`tree`——这几乎涵盖了所有的可能性，因为在那些限制下，没有更多的方法来显示那种结构。好在`tree`自带便利设施，[从模式匹配到 JSON 输出](https://www.systutorials.com/docs/linux/man/1-tree/)。

我也做了很多包含我不关心的某些文件的项目。使用 git，我在项目根目录中使用一个 [`.gitignore`](https://git-scm.com/docs/gitignore) 文件来确保我不会意外地添加和提交它们。这个文件不仅被 git 使用:我选择的搜索工具， [ripgrep](https://github.com/BurntSushi/ripgrep) ，尊重`.gitignore`规则，就像许多其他工具一样，一直到图形化 ide。

最少比 git 早十年的`tree`，不关心你的`.gitignore`。当使用中等大小的忽略规则集和/或类似于`node_modules`的东西检查存储库的布局时，这使得它几乎不可用。

`tree`的特性之一是`-I`标志，它会忽略与`.gitignore`中使用的通配符模式相匹配的文件。这意味着应该有可能把遵守`.gitignore`规则的东西拼凑在一起，而不需要在 coreutils 中瞎折腾:其他系统工具输出和操作文件，`xargs`可以管理其他命令的参数，管道把整个东西钩在一起。

这里是我的`.zshrc`的完整别名，如果你只是对那部分感兴趣的话(注意，所有的都需要在一行):

```
alias trii="(cat .gitignore & echo '.git') |
  sed 's/^\(.\+\)$/\1\|/' |
  tr -d '\n' |
  xargs printf \"-I '%s'\" |
  xargs tree -C" 
```

Enter fullscreen mode Exit fullscreen mode

除了`-I`，您仍然可以将`tree`的参数传递给`trii`，因此工具包的其余部分仍然可用。如果当前目录中没有忽略文件，这也是安全的。

现在，更深入地说:

```
(cat .gitignore & echo '.git') 
```

Enter fullscreen mode Exit fullscreen mode

`cat`将忽略文件转储到标准输出(控制台)，而`echo`只是重复字符串”。git”来确保完整的规则集不包括存储库目录本身(只有显示隐藏文件和目录的`-a`开关有问题)。单个`&`只是一个分隔符，以确保两个命令按顺序运行，而不是更常见的双`&&`，它在第一个非零退出代码时中止。括号在一个子 shell 中运行全部内容，返回完整的输出，以便通过管道传输到下一个部分。

```
sed 's/^\(.\+\)$/\1\|/' 
```

Enter fullscreen mode Exit fullscreen mode

不能指定多个`-I`值:最后一个总是赢。相反，`-I`可以读取用管道`|`字符连接在一起的多个模式。这是可能的，但是需要几个步骤。

`sed`是一个**s**stream**ed**itor，它修改来自前一段的每一行。这里，它只是添加了管道字符。因为`sed`作为一个离散的实体对每一行进行操作，所以它不能将它们连接在一起；这是下一个环节:

```
tr -d '\n' 
```

Enter fullscreen mode Exit fullscreen mode

与`sed`不同的是，`tr` ( **tr** anslate)在标准输入进来时对其进行操作，而不是逐行操作。开关`-d`删除字符，这里是换行符。这就完成了 ignore 模式，一个样本项目的`.gitignore`转换成了这个:

```
.git|src|pkg|**/*.tar.xz| 
```

Enter fullscreen mode Exit fullscreen mode

有终接管道，但对`tree`没有影响。这一行被传递给另一个命令:

```
xargs printf "-I '%s'" 
```

Enter fullscreen mode Exit fullscreen mode

将标准输入中的行传递给另一个命令。这里只有一行，因为`tr`删除了所有的换行符，它被传递给`printf`。这不要与 C 标准库函数`printf`混淆:它是 GNU coreutils 中的一个独立程序，尽管它做的事情和它的近亲差不多。该命令的实际效果是将`-I`开关*和*串联的忽略列表打印在一起。

```
xargs tree -C 
```

Enter fullscreen mode Exit fullscreen mode

最后，是时候调用`tree`！`-C`标志为输出添加颜色。`xargs`将组合的`-I`和 ignorelist 传递到命令字符串中，结果是一个`tree`，它排除了`.gitignore`中的所有内容。