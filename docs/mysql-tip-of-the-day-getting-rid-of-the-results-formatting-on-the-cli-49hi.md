# MySQL 日积月累:消除 CLI 中的结果格式化

> 原文：<https://dev.to/fgm/mysql-tip-of-the-day-getting-rid-of-the-results-formatting-on-the-cli-49hi>

*   mysql 上的更多帖子

### 问题

当从 CLI 使用 MySQL 时，`-e`标志可以方便地直接在 bash 中执行 MySQL 命令，如`mysql -uroot -p -e "SHOW DATABASES;"`。这给出了格式良好的输出，如:

```
+-----------------------+
| Database              |
+-----------------------+
| drop_7                |
| drop_8                |
| pressflow             |
(...snip...)
+-----------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

问题是这个表格图占据了屏幕上的空间。没有办法摆脱吗？

### 解

第一个想法是检查 MySQL 命令分隔符。例如`\G`给了我们这个:

```
mysql -uroot -p -e "SHOW DATABASES\G"
*************************** 1\. row ************************** 
drop_7 
*************************** 2\. row ************************** 
drop_8 
*************************** 3\. row **************************
pressflow(...snip...) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们去掉了标题，取而代之的是行分隔符。这里并不是真正的净收益！当然我们可以使用一个

`| grep -vF "row **************************"`

但这并不方便，甚至可能导致某些查询的误报。我们能做得更好吗？

事实证明我们可以。当发出输出时，`mysql`客户机检查输出是否是 TTY，并使用该信息发出装饰性的环绕结果。如果输出是*而不是*一个 TTY，它只输出普通的、可预测的结果。这给了我们一个很好的解决方案，用可靠的老`cat(1)` :

```
mysql -uroot -p -e "SHOW DATABASES;" | cat
Database
drop_7
drop_8
pressflow
(...snip...) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有所进展:将`mysql` stdout 通过管道传输到另一个命令，使得它不再被检测为 TTY，因此不再使用装饰。当然，我们也可以只使用`-s`选项来获得该格式，但是这里的重点是要注意 TTY 与管道。在这两种情况下，剩下的只是标题(这里是“数据库”)。也容易移除。

```
mysql -uroot -p -e "SHOW DATABASES;" | tail +2
drop_7
drop_8
pressflow
(...snip...) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。`tail`的`+2`选项使其从第 2 行开始输出，跳过标题。没有长命令，没有误报。

结束语:根据 [man tail](http://man7.org/linux/man-pages/man1/tail.1.html) 的引用，该命令实际上应该是`tail -n +2`，但是我所知道的所有版本的 tail 在那种情况下实际上都不需要`-n`。