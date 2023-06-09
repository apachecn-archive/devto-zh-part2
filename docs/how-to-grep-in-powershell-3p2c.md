# 如何在 PowerShell 中“grep”

> 原文：<https://dev.to/antjanus/how-to-grep-in-powershell-3p2c>

如果你没有使用过 PowerShell，它是很棒的，但是如果你习惯了 Unix 风格的 Bash 风格的 Shell，它绝对是陌生的。例如，没有`grep`，`ls -al`不起作用(但是`ls`会，它会显示所有文件的细节！).

如果您从 Linux 切换到 Windows，您可能会尝试安装 Cygwin 或所有的 GNU 实用程序。或者你可以走 Linux 子系统的路线(这是一条完全公平的路)，但是如果你想走 PowerShell 的路线，这里有一个小技巧！

## grep 是什么？

Grep 基本上是一个实用程序，允许您使用各种模式过滤一些数据。还有很多，但我自己的常用用法是:

```
$ cat .bash_history | grep ssh
ssh root@0.0.0.0
ssh deploy@0.0.0.0
ssh ubuntu@0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

这将过滤掉`cat`的输出，只返回包含`ssh`的命令。我用这个来提醒自己最近用过的服务器的 IP。或者我可以这样做:

```
$ cat package.json | grep webpack
    "start:dev": "webpack --config webpack/dev.config.js",
    "webpack": "3.8.1",
    "webpack-dev-server": "^2.9.4",
    "webpack-merge": "^4.1.1", 
```

Enter fullscreen mode Exit fullscreen mode

获取我所有的 webpack 插件、脚本，并找出 webpack 的最新版本。这是一个方便的工具，我一直在使用它。过滤文件夹中的文件列表，并通过管道将它们发送到另一个命令或其他任何命令。

Grep 功能强大，非常有特色，但是我不会在这篇文章中深入讨论。

那么 PowerShell 有什么等价的呢？

## 引入选择字符串

PowerShell 有一点非常明显，那就是它的命令有更好的命名约定。其实 PS script 比 Bash 更像一种编程语言。

PowerShell 管道类似于 Bash 管道，虽然它有一些不同之处，但我现在不会深入讨论。

```
cat package.json | Select-String -Pattern webpack
ls ./src/components/ | Select-String -Pattern View 
```

Enter fullscreen mode Exit fullscreen mode

Select-String 支持正则表达式，所以你也可以写更复杂的东西，比如:

```
C:\> cat post.md | Select-String -Pattern "^\w*:"

title: How to "grep" in PowerShell
published: false description:
tags: 
```

Enter fullscreen mode Exit fullscreen mode

这将从这篇文章中提取出最重要的内容(在写这一部分的时候)。

## 在多个文件中搜索怎么样？

绝对的！Grep 有一个非常简单的语法:`grep $PATTERN glob/pattern/**`。一个`-r`标志将递归搜索路径。

你可以用它来识别你在角度应用中使用`@select`的地方。`@select`是 angular-redux 商店提供的装饰器。

```
$ grep -r "@select" *.ts
comments-list.component.ts:  @select(['comments']) comments: Observable<IComment[]>;
comments-list.component.ts:  @select(authorsSelector) authors: Observable<any>;
comments-list.component.ts:  @select(['topComment']) topComment: Observable<IComment>; 
```

Enter fullscreen mode Exit fullscreen mode

那么 PowerShell 呢？在 PowerShell 中，您必须指定一个参数，但是这种明确性实际上很有意义:

```
C:\> Select-String -Path *.ts -Pattern "@select"

comments-list.component.ts:51:  @select(['comments']) comments: Observable<IComment[]>;
comments-list.component.ts:53:  @select(authorsSelector) authors: Observable<any>;
comments-list.component.ts:55:  @select(['topComment']) topComment: Observable<IComment>; 
```

Enter fullscreen mode Exit fullscreen mode

酷，那么递归呢？犯罪...好吧，要让真正的递归工作并扫描“n”层深度，你需要把几个命令串在一起，所以我们现在跳过它。

# 快乐的 PowerShelling！

### 高级例子

在我离开之前，我想传授一些有趣的高级命令。如我所说，PowerShell 管道不同于 Linux 管道。其中一个很大的区别是 PowerShell 利用了“对象”。

这里有一个很好的例子:

```
C:\> Select-String -Path *.ts -Pattern "@select" | Select Filename, LineNumber, Line, Path | Format-Table 
```

Enter fullscreen mode Exit fullscreen mode

这将做的是:在我们当前目录中的所有 TypeScript 文件中找到与我们的模式匹配的文件，只选择我们想要查看的信息，并将其转换成一个好看的表格，如下所示(编辑以适应站点宽度):

```
Filename                    LineNumber   Line               Path
--------                    ----------   ----               ----
comments-list.component.ts  51           @select(['comme... C:\comments-list...
comments-list.component.ts  53           @select(authors... C:\comments-list...
comments-list.component.ts  55           @select(['topCo... C:\comments-list... 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以通过`Out-File <filename>`把它保存到一个文件中，就像这样:

```
C:\> Select-String -Path *.ts -Pattern "@select" | Select Filename, LineNumber, Line, Path | Format-Table | Out-File selectors.txt 
```

Enter fullscreen mode Exit fullscreen mode

或者输出到 CSV:

```
C:\> Select-String -Path *.ts -Pattern "@select" | Select Filename, LineNumber, Line, Path | Format-Table | Export-Csv -path selectors.csv -NoTypeInformation 
```

Enter fullscreen mode Exit fullscreen mode

我*喜欢【PowerShell 的地方在于它是内置的，而且它有着明智的名字。当我阅读 bash 脚本时，可能会感到困惑。我是说，`awk`是做什么的？那`sed`呢？你能根据他们的名字推断出他们的工作吗？或者他们的旗帜意味着什么？*