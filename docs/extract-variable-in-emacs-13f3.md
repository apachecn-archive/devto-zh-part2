# 提取 Emacs 中的变量

> 原文：<https://dev.to/jplindstrom/extract-variable-in-emacs-13f3>

如果您正在使用 Emacs 争论 Perl，这里有一个有用的重构特性可供您使用:

*提取变量* - `C-o e e v` -做重构*提取活动区域的变量*。

安装说明如下。

## 什么是重构来着？

马丁·福勒在他 1999 年的书《T2 重构:改进现有代码的设计》中介绍了重构的概念。

重构是一种在保留行为的同时，重构代码以使其更容易操作的方法。重构发生在可预测的小步骤中，每个步骤之后都有测试通过。

```
 "your system should not be broken
  for more than a few minutes
  at a time"
                   -- Martin Fowler 
```

从那以后，这个词的原意变成了“意思”...实际上，对现有代码的任何旧的重写，不管它的规模有多大，也不管它是否通过修复一个 bug 或改变需求来改变行为。

这些都是你可以对代码库做的合法的事情，但是这不是 Fowler 描述的活动。

## 提取变量

Extract Variable 是实现起来最简单、最小的重构之一，但在日常编程中仍然非常有用。任何有助于使代码在行或方法级别上更清晰的东西都是受欢迎的帮助。

它也很容易操作，因为它只有一个步骤。

## 走查

下面是一个例子，其中有一段代码访问应用程序用户和 db admin 用户的数据库配置。大多数都是一样的。

```
my $app     = $self->app->config->{services}->{db}->{product}->{app};
my $dbadmin = $self->app->config->{services}->{db}->{product}->{dbadmin}; 
```

选择一段在当前子程序中重复很多的代码(在任一行上)并点击`C-o e e v`(助记符:编辑-提取变量)。

在这种情况下，这似乎是共同的部分:

```
$self->app->config->{services}->{db}->{product} 
```

您将被要求输入一个变量名来放入这个变量。默认为所选代码的最后一个字(`$product`)。

所有出现的选择现在都将被替换为`$product`，新变量`$product`将在最早使用之前声明。

```
my $product = $self->app->config->{services}->{db}->{product};
my $app     = $product->{app};
my $dbadmin = $product->{dbadmin}; 
```

在编辑之前，`mark`被推到了你开始的位置，你可以点击`C-u C-SPC`跳回去。

编辑后，该点留在新变量声明处，这样您可以确保它在一个合理的位置。为了让声明覆盖所有的用法，通常需要将它移到外部范围。

现在你需要确保这个编辑是有意义的。替换和声明都被突出显示，所以很容易看出什么被改变了。

如果都是错的，只需`M-x undo`即可恢复更改。

一旦你看清了编辑的内容，点击`C-o e h`删除高亮部分。

请注意，替换是不知道语法的，所以您必须自己确保它在语法上是正确的，并放在源代码中的合适位置(尽管大多数时候它工作得很好)。

在这个特殊的例子中，如果散列键之间没有箭头，最终的代码应该是这样的:

```
my $product = $self->app->config->{services}{db}{product};
my $app     = $product{app};
my $dbadmin = $product{dbadmin}; 
```

这显然不是等价的 Perl 代码，`$product` *的 hashef*被视为*的 hash* 。这可能是最常见的故障模式，不应该经常发生。现在你知道了。

默认情况下，只更改当前的子例程。使用前缀 arg `C-u`调用以更改整个缓冲区:`C-u C-o e e v`。

## 用于提取变量的整齐用途

#### 重复

删除重复的代码(duh)，因为小规模的复制只是粗制滥造，这为引入描述什么是*什么是*的良好命名的变量提供了机会。

#### 长长的线条

通过提取一个临时变量，将一长串不可读的行分成两行。当 if 语句的条件过长时，这尤其有用。

#### 重命名

重命名变量——根据变量名提取变量，然后删除新的变量声明。

```
my $books = $schema->resultset("Book");
my @books = $books->search({ ... })->all; 
```

我们把`$books` - > `$book_rs`重新命名吧。*在`$books`上提取变量*将此转化为:

```
my $book_rs = $books;                      # <-- delete this line
my $book_rs = $schema->resultset("Book");
my @books = $book_rs->search({ ... })->all; 
```

点现在在第一个声明上，所以删除那一行就行了。

#### 字符串

在字符串中进行方法调用:

```
print "So, you want to make a $object->method_call inside a string\n"; 
```

但这显然行不通。有各种各样的方法可以解决这个问题，但是你可以做的一件事就是标记`$object->method_call`来提取它，并以下面的代码结束:

```
my $method_call = $object->method_call;
print "So, you want to make a $method_call inside a string\n"; 
```

不错！

## 重述

*   选择要提取的重复代码。它应该在语法上准备好被赋给变量
*   `C-o e e v`提取变量
*   确认变量名或输入一个更好的名称
*   请确认摘录有意义，或者修复代码
*   `C-o e h`通过删除突出显示来结束重构

## 获取软件

*提取变量*在 [Devel::PerlySense](https://metacpan.org/pod/Devel::PerlySense) 中可用，这是 Emacs 的 Perl IDE。大多数演练示例实际上来自 Devel::PerlySense 的 POD。上面描述的键绑定就是 PerlySense 的现成内容。

安装您的常规 CPAN 客户端，并查看[配置部分](https://metacpan.org/pod/Devel::PerlySense#INSTALLATION)了解如何设置它的详细信息。这也会安装所有的 elisp 代码。

*提取变量*都是在 Elisp 中实现的，所以如果不想安装 PerlySense，可以改为安装包含这一个函数的 *Melpa* 包 [lang-refactor-perl](https://melpa.org/#/lang-refactor-perl) 。如果这样做，您将不得不创建自己的键绑定。

酷，现在去清理一些代码！:)