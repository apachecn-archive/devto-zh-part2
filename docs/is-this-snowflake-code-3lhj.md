# 这是雪花码吗？

> 原文：<https://dev.to/jacoby/is-this-snowflake-code-3lhj>

这是从点击 yapc 的 IRC 通道中的一个链接开始的，这个链接是关于 Perl 的，但是我不认为这一点是非常特定于语言的。

链接是 Dave Rolsky 的[Please Don ' t Write snow flake Code](https://blog.urth.org/2012/10/07/please-dont-write-snowflake-code/)，这是六年前的作品，并不意味着(我不相信)挖苦今天被庇护的孩子，而是为一个开发与编码社区的其他人隔离的偏好的程序员。

> 除了原型问题，这些都没有错(原型可以说是文档)。代码像写的那样工作，意思或多或少是清楚的。
> 
> 但这很奇怪，因为大多数人不这么做。这是一片独特的雪花。
> 
> 这是一种干扰。
> 
> 有经验的 Perl 程序员不会看代码，而是会花时间去“思考”为什么这段代码看起来与他们见过的所有其他代码都不一样。

我明白了。当我查看和参与其他人的项目时，我会尝试按照他们的惯例进行编码，并且我不会对 Javascript、CSS、HTML 和我可能安装的任何其他东西的 VS 代码格式化程序进行自定义更改。SQL？

但是我有 19 行 perltidyrc，这非常接近我想看到的:

```
 --backup-and-modify-in-place
    --block-brace-tightness=0
    --brace-tightness=0
    --closing-token-indentation=3
    --continuation-indentation=4
    --indent-block-comments 
    --indent-closing-brace  
    --indent-columns=4
    --maximum-line-length=100
    --no-outdent-long-quotes
    --noopening-sub-brace-on-new-line 
    --opening-brace-always-on-right
    --paren-tightness=0
    --space-for-semicolon
    --space-terminal-semicolon
    --square-bracket-tightness=0
    --stack-opening-tokens
    --vertical-tightness-closing=0
    --vertical-tightness=0 
```

Enter fullscreen mode Exit fullscreen mode

我是从[开始的。perltidyrc](https://gist.github.com/kimmel/1305940) 来自 *[Perl 最佳实践](http://shop.oreilly.com/product/9780596001735.do)* ，第一次更改是冗长的，因为在多年不更改 rc 文件之后，您永远也不会记得`-cti=0`或`-bt=1`意味着什么。

但是我在开始使用 Perl 之前很久就开发了我喜欢的缩进风格，在读 CS 本科时，我学习了早期的 C-as-subset-of-C++课程。这非常接近拉特利夫风格。从我几年前写的强力数独代码中，我用一个空的配置文件整理了一下，然后用我喜欢的风格整理了一下。

```
# default style
use strict;
use warnings;
use feature qw{ say postderef signatures };
no warnings qw{ experimental::postderef experimental::signatures };
use subs qw{ solve_sudoku test_solution display_puzzle no_go_list };

use Data::Dumper;

my @array;
my @test;
my $x = 0;
my $debug = 0;
my $outcount = 0;

#READ IN DATA
while ( my $line = <DATA> ) {
    chomp $line;
    $line =~ s{\D}{ }mxg;
    my @line = split m{|}mx, $line;
    for my $y ( 0 .. 8 ) {
        my $digit = '  ';
        $digit = $line[$y] if defined $line[$y];
        $array[$x][$y] = $digit;
    }
    $x++;
}
solve_sudoku( 0, 0, '' ); 
```

Enter fullscreen mode Exit fullscreen mode

```
# my preferences
#!/usr/bin/perl

use strict ;
use warnings ;
use feature qw{ say postderef signatures } ;
no warnings qw{ experimental::postderef experimental::signatures } ;
use subs qw{ solve_sudoku test_solution display_puzzle no_go_list } ;

use Data::Dumper ;

my @array ;
my @test ;
my $x = 0 ;
my $debug = 0 ;
my $outcount = 0 ;

#READ IN DATA
while ( my $line = <DATA> ) {
    chomp $line ;
    $line =~ s{\D}{ }mxg ;
    my @line = split m{|}mx, $line ;
    for my $y ( 0 .. 8 ) {
        my $digit = '  ' ;
        $digit = $line[$y] if defined $line[$y] ;
        $array[$x][$y] = $digit ;
        }
    $x++ ;
    }
solve_sudoku( 0, 0, '' ) ; 
```

Enter fullscreen mode Exit fullscreen mode

我看到的主要区别是分号后的空格(或缺少空格)以及右括号是否与它们所包含的代码缩进。哦，是的，如果数组索引的括号紧不紧。

我见过栈溢出的人把我的代码重新编辑成无法运行的东西，因为他们非常讨厌我的缩进，这也是我不再尝试帮助他们的部分原因。但我不认为这些太过分了。

那么，这个奇怪的缩进是不是除了戴夫谁都看不懂？还是我杞人忧天了？

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。