# 安装的 Perl 模块的版本是什么？

> 原文：<https://dev.to/jplindstrom/whats-the-version-of-that-installed-perl-module-m04>

这里有一个简单的技巧来找出安装了哪个版本的 Perl 模块。

我们用`Carp`试试。

```
perl -MCarp\ 99999 -e 1

Carp version 99999 required--this is only version 1.42.
BEGIN failed--compilation aborted. 
```

其工作方式是，我们要求运行时加载一个非常高的版本，然后等待`perl`道歉说它只有当前版本 x，在本例中是`1.42`。

注意模块和版本之间的转义空格:
`-MCarp\ 99999`。