# 我没有从 Perl 6 中窃取的东西

> 原文：<https://dev.to/ilya_sher_prog/what-i-did-not-steal-from-perl-6-5b0g>

我对编程语言很好奇。不是因为我现在正在创造一个。我一直都是。这篇文章是关于我在 Perl 6 中看到的有趣的想法和特性。如果你对编程语言很好奇，你应该看看这些。

不从 Perl 6 中窃取有趣的想法有多种原因:

1.  我试图让 NGS 的概念数量尽可能少。如果我在一个概念中看不到巨大的直接价值，我会跳过它。
2.  不接受任何我认为会让我或其他程序员困惑的东西。我在这里不说话是因为有人是初学者。我说的是混淆概念。
3.  仅仅是因为我目前没有足够的资源来实施它。

以下是有趣的 Perl 6 特性，排名不分先后(除了第一个)。也有我的意见，我是否喜欢在 NGS 的功能或为什么不。

1.  [语法](https://docs.perl6.org/language/syntax)。非常有表现力和简洁。Perl6 甚至比 Perl 5 拥有更多。既然我们已经摆脱了房间里的`$`和朋友:
2.  [语法](https://docs.perl6.org/language/grammars)。在 NGS 有这样的东西真的很好。
3.  大量的[操作符](https://docs.perl6.org/language/operators)。最有趣的概念是[元运算符](https://en.wikibooks.org/wiki/Perl_6_Programming/Meta_Operators)。我试图在 NGS 保持相对较少的语法元素。NGS 已经有两种语法:命令和表达式。没有必要时不要使用更多的语法。
4.  [“尖块”语法](https://en.wikibooks.org/wiki/Perl_6_Programming/Blocks_and_Closures)如何与“for”语法:`for @list -> @element`。NGS 已经有了几种 Lambdas 的语法。
5.  流控制
    1.  [当](https://docs.perl6.org/language/control#when)流量控制。最接近 NGS 的是从 Lisp 偷来的“cond”和朋友。
    2.  [重复 while /重复直到](https://docs.perl6.org/language/control#repeat/while,_repeat/until)。在 NGS 有这样的东西就好了。
    3.  [一次](https://docs.perl6.org/language/control#once)。这个不确定。可能需要该功能。
6.  [卡瓦](https://docs.perl6.org/language/list#Slips)。这种行为让我害怕:如果它确实扩展了，如果我只是想传递一个 Slip，比如说作为数组的一个元素，我该如何传递它呢？NGS 使用滑:`[1, 2, *myitems, 3, 4]`的语法，我认为这样更干净。你知道你不能通过它，因为它是语法。
7.  [。什么](https://docs.perl6.org/language/objects#Type_objects)法。我从 Ruby 那里偷了一些类似的东西:`inspect`方法。

特别说明一下，我看到了从`$arr[0]`到`@arr[0]`的可喜变化。我认为它消除了困惑。(那是 Perl 5 对 Perl 6)。

如果您是一名 Perl 6 黑客，并且您看到了我没有提到的惊人特性，请不要生气。这可能是因为我已经在其他几种语言中看过了，或者可能我觉得它没什么意思，或者……可能我只是错过了它。无论如何不要犹豫留下评论。

* * *

快乐编码，不管用什么语言！除了巴什。bash 中的编码永远不会快乐。