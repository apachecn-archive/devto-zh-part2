# 每周命令:用 diff 逐行比较文件

> 原文：<https://dev.to/roperzh/weekly-command-comparing-files-line-by-line-with-diff-2081>

## 基础知识

```
diff [*option*] [*files*]
```

`diff`逐行输出两个文件之间的差异。对于相同的文件，它不产生输出，对于二进制文件，只报告它们是否不同。

由`diff`产生的一组差异通常被称为*差异*或*补丁*，这个输出可以在以后被 [`patch`](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/patch.html) 命令用来改变其他文件。

## 逐剧播放

在深入研究示例之前，让我们定义两个文件，这将有助于我们阐明一些概念。

为了保持尽可能低的概念开销，文件用`with-text.js`和`without-text.js`来表示`text`变量是否存在。

文件`with-text.js`:

```
function example (text) {
  console.log("====")
  console.log(text)
} 
```

文件`without-text.js`:

```
function something () {
  console.log("====")
} 
```

### 默认输出

默认情况下，`diff`输出文件之间不同的所有行的列表，一个接一个，旁边还有有用的信息，说明发生了什么变化。变化可以是:

*   一个附加项，用字母`a`表示。
*   由字母`d`表示的删除。
*   一个变化，用字母`c`表示。

对于`diff`发现变化的每一行，它输出:

*   什么类型的变化是(`a/c/d`)
*   每个文件中受影响的行号是什么
*   受影响的行的内容

在一个更具体的例子中，如果您运行:

```
$ diff with-text.js without-text.js

1c1
< function example (text) {
---
> function something () {
3d2
<   console.log(text) 
```

您将获得:

*   `1c1`表示两个文件的第`1`行有一个`c`的改变。
*   `< function example (text) {`表示线条在`with-text.js`中的样子。
*   `> function something () {`表示线条在`without-text.js`中的样子。

您能推断出输出中其余的行表达了什么吗？

### 并排比较

除了默认的输出格式之外，您还可以指定`--side-by-side`(简称`-y`)来生成一个并排的视图，显示所做的更改。

这种格式更直观，乍一看更容易理解，例如:

```
$ diff with-text.js without-text.js --side-by-side

function example (text) {             | function something () {
  console.log("====")                     console.log("====")
  console.log(text)                   <
}                                       } 
```

### 处理目录

`diff`的一个非常强大的特性是能够处理目录。需要注意的是，目录的实际内容从来不会像文件一样进行比较，相反`diff`使用以下规则:

*   如果一个文件是目录，而另一个不是，`diff`比较目录中与非目录同名的文件。
*   如果给出了两个文件名并且都是目录，`diff`按字母顺序比较两个目录中的相应文件。

### 彩色化输出

从版本 3.4 ( [在 2016 年 8 月左右推出](https://savannah.gnu.org/forum/forum.php?forum_id=8639))开始，`diff`支持在终端打印`--color`标志彩色输出。

### 读数标准输入

如果您提供`-`作为文件名，`diff`将使用它作为从标准输入中读取的文本。作为一个特例，`diff - -`将标准输入的副本与自身进行比较。

### 省略差异

`diff`还提供了消除文件间差异的方法，这些差异对您来说可能并不重要，常见的例子是改变单词或行之间的空白量。

| 旗 | 描述 |
| --- | --- |
| -我-忽略-案例 | 忽略文件内容的大小写差异。 |
| -忽略-文件名-大小写 | 比较文件名时忽略大小写。 |
| - no-ignore-file-name-case | 比较文件名时要考虑大小写。 |
| -E -忽略-选项卡-扩展 | 忽略由于选项卡扩展而导致的更改。 |
| -b -忽略-空格-更改 | 忽略空白量的变化。 |
| -w -忽略所有空格 | 忽略所有空格。 |
| -B -忽略空白行 | 忽略所有行都是空白的更改。 |
| -I RE -忽略-匹配行=RE | 忽略所有行都匹配 re 的更改。 |

## 使用合适的工具进行工作

虽然很棒，但它并不总是适合这项工作的工具，以下是一些更适合不同情况的工具:

*   `diff3`命令可以用来显示三个文件*之间的差异。*
*   对于二进制文件，`cmp`逐字节报告两个文件的差异，而不是逐行报告。
*   如果你只是寻找视觉上的不同而没有修补，`vim -d`和`git diff`做得很好。