# 比较目录:案例研究

> 原文：<https://dev.to/dmerejkowsky/comparing-directories-a-case-study-32hn>

*最初发表于[我的博客](https://dmerej.info/blog/post/comparing-directories/)。*

# 问题

假设我们有一个名为 O 的目录，其中包含大量子目录和文件。

我们发现自己有两个不同的目录，A 和 b。每个目录都是从 O 的副本开始的，但是，在每个目录中，有些文件被更改，有些被添加或删除，我们不知道是哪些文件。

我们想要比较 A 和 B 的内容，例如将它们合并回 o。

解决问题的方法有几种，看情况而定。让我们来看看。

# 使用图形用户界面

对于我们的第一个例子，让我们假设 A 和 B 在我们的开发计算机上，并且我们打开了一个图形会话。

我们可以使用许多 GUI 工具来完成这项任务，这里有一个使用 [Kdiff3](http://kdiff3.sourceforge.net/) 的例子:

[![kdiff3 screenshot](../Images/a1a6a0f07d774b77c7061749d22fe34c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pLL8XV4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/kdiff3.png)

在左上方的窗格中，我们看到了 A 和 B 目录之间所有差异的概述，我们可以看到:

*   在 A 和 B 之间,`bar/eggs`和`bar/spam`目录是相同的
*   中有一个名为`bar/new.txt`的新文件
*   B 中有一个名为`baz/six.txt`的新文件
*   文件`foo/a.txt`在 A 和 B 中均被更改

在底部窗格中，由于选择了`a.txt`，我们可以看到内容的不同。

# 使用命令行

现在让我们假设 A 和 B 都在运行 Linux <sup id="fnref1">[1](#fn1)</sup> 的远程服务器上，我们不能在那里打开远程图形会话。

我们有什么选择？

嗯，我们可以使用一个 *coreutils* 命令。

Coreutils 是一个非常古老的包，几乎存在于任何 Linux 操作系统中。它包含一个有用的小命令行程序的集合。即使没有安装，其他包(如`busybox`)通常也会提供类似的功能。

无论如何，有一个名为`diff`的 coreutils 命令可以用于手头的任务:

```
$ diff --recursive a b
Only in a/bar: new.txt
Only in b/baz: six.txt
diff --recursive a/foo/a.txt b/foo/a.txt
1c1
< this is a
--------
> this is modified a 
```

Enter fullscreen mode Exit fullscreen mode

嘣！我们需要的所有信息。

`diff`提供了很多方法来调整它的行为 <sup id="fnref2">[2](#fn2)</sup> 。例如，我们可以使用`--brief`选项:
获得更简洁的信息

```
$ diff --recursive --brief a b
Only in a/bar: new.txt
Only in b/baz: six.txt
Files a/foo/a.txt and b/foo/a.txt differ 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，如果 git 安装在远程服务器上，我们也可以使用它，有很好的选项，如`--stat`或`--word-diff` <sup id="fnref3">[3](#fn3)</sup>

```
$ git diff --stat a b
 a/bar/new.txt => /dev/null | 1 -
 /dev/null => b/baz/six.txt | 1 +
 {a => b}/foo/a.txt | 2 +-
 3 files changed, 2 insertions(+), 2 deletions(-) 
```

Enter fullscreen mode Exit fullscreen mode

# 远程目录

现在让我们假设 A 和 B 在两个不同的远程服务器上:让我们分别称他们为 T2 修道院院长和 T4 科斯特洛。

此外，让我们假设 A 和 B 包含许多大文件，所以我们不能将 A 目录从 abbot 复制到 costello 并使用前面的技术。但是，我们可以在两台服务器之间传输小文件。这是我们能做的。

首先，我们登录 abbot:

```
ssh user@abbot 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们进入`a`目录:

```
cd a 
```

Enter fullscreen mode Exit fullscreen mode

然后我们执行下面的命令:

```
find . | sort > ~/manifest-a 
```

Enter fullscreen mode Exit fullscreen mode

*解释*:

*   列出当前工作目录下的所有文件和目录。
*   find 返回的文件顺序是不确定的，可以从一个文件系统改变到另一个文件系统，所以我们使用管道(`|`)获取`find`的输出，并将其传递给`sort`命令。(`sort`也是 coreutils 包的一部分)。
*   最后，我们用一个尖括号(`>`)将`sort`的输出写到主目录中的一个文件:(`~/manifest-a`):我们一定不能将 manifest——一个文件写到`a`目录中，否则 manifest 可能包含它自己！

现在，我们对科斯特洛做同样的事情:

```
ssh user@costello
cd b
find . -type f | sort > ~/manifest-b 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用`scp`将 A 清单传输给科斯特洛:

```
# On abbot
scp manifest-a user@costello: 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个关于科斯特洛的文本文件，都包含所有文件的列表。因此，我们对清单文件本身使用`diff`:

```
diff ~/manifest-*
...
-./bar/new.txt
...
...
+./bar/six.txt 
```

Enter fullscreen mode Exit fullscreen mode

它负责目录的内容。文件本身的内容呢？

嗯，我们可以使用另一个叫做`shasum`的 coreutils 工具。

`shasum`可用于计算给定文件内容的*校验和*。如果内容相同，它将生成相同的输出，如果两个文件不同，它们的校验和几乎不可能相等。 <sup id="fnref4">[4](#fn4)</sup>

我们可以用文件列表作为参数调用`shasum`，就像这样:

```
shasum file1.txt file2.txt 
```

Enter fullscreen mode Exit fullscreen mode

这里我们需要用整个文件列表调用 shasum，所以我们再次调用`find .`，但是用`-type f`选项只选择文件而忽略目录。这给了我们一列*行*，所以我们使用`xargs`(也在 coreutils 中)将它们转换成一列命令行参数 <sup id="fnref5">[5](#fn5)</sup> ，最后将结果写入一个`a.shasum`文件:

```
find . -type f | xargs shasum > ~/a.shasum 
```

Enter fullscreen mode Exit fullscreen mode

下面是`a.shasum`文件的内容:

```
791c4ba196e0faea35e0c5fbe46e64da bar/eggs/two.txt
23f4a2592b2e4dee0444983a6e53c23e bar/new.txt
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将`a.shasum`文件传输到科斯特洛:

```
$ scp a.shasum user@costello: 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以通过`--check`选项再次使用`shasum`:它将读取每一行，解析文件名和期望的校验和，然后计算给定文件的*实际的*校验和，并检查它是否与期望值匹配:

```
$ cd b
$ shasum --check ~/a.shasum
./bar/eggs/two.txt: OK
./bar/spam/one.txt: OK
...
./foo/a.txt: FAILED
...
shasum: WARNING: 1 of 5 computed checksums did NOT match 
```

Enter fullscreen mode Exit fullscreen mode

我们完成了:从清单的差异中，我们知道哪些文件丢失或被添加，从校验和列表中，我们知道哪些文件不同。

# 结论

我们看到我们只能使用来自`coreutils`包的命令行工具来比较两个目录的内容:我们使用的技术在几十年前以完全相同的方式工作，并且可能会继续工作很长时间。

所以，如果你喜欢这里展示的技术，这里是我给你的建议:下次当你面对一个类似于我们刚刚学习的任务时，看看所有的 coreutils 文档，并尝试使用它们。如果你经常这样做，过一段时间后，你会获得一个不错的技能组合。

哦，如果你认为“这些都没用”，请阅读这篇名为*的[启发性论文](https://adamdrake.com/command-line-tools-can-be-235x-faster-than-your-hadoop-cluster.html)，命令行工具可以比你的 Hadoop 集群快 235 倍*:)

干杯！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  我听说有人在远程服务器上运行 Linux 以外的操作系统。如果你是这种情况，我很抱歉。 [↩](#fnref1)

2.  如果你想看看所有的`diff`能做什么，请随意运行`man diff`并感到惊讶！ [↩](#fnref2)

3.  Git 足够聪明，可以看出 a 和 b 都不在它控制的目录中，如果 a 和 B *在 git 工作树中*，你可以使用`--no-index`选项，正如文档<sup>【return】[【↩](#fnref3)</sup>中所解释的

4.  你可能已经听说过 *md5 sum* :它的工作方式完全相同，但使用一种更老的算法来计算校验和，通常不鼓励使用它。 [↩](#fnref4)

5.  如果你使用 zsh，你也可以让它直接列出所有扩展名为 *glob* 的文件，比如:`shasum **/*(.) > ~/a.md5`。耶 zsh！ [↩](#fnref5)