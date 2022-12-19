# 因为最后一个严重缺乏高阶代码…

> 原文：<https://dev.to/jacoby/since-that-last-one-was-severely-without-higher-order-code-50d9>

我从书架上取下马克-杰森·多明纳斯写的《高阶 Perl》,在里面看了看。([网上免费](https://hop.perl.plover.com/#free))。)

> 高阶编程是一种计算机编程风格，它使用软件组件，如函数、模块或对象，作为值。它通常是用计算模型来实例化的，或者借用计算模型，比如大量使用高阶函数的 lambda 演算。

我扔在一起的第一个东西只是稍微改编了一下 *HOP* ，取一个‘名字’和初始值，打印名字和当前值。

```
#!/usr/bin/env perl

use strict ;
use warnings ;
use utf8 ;
use feature qw{ postderef say signatures state } ;
no warnings qw{ experimental::postderef experimental::signatures } ;

my $dis ;

# fill the dispatch table
for my $i ( 0 .. 9 ) { $dis->{ $i } = maker( $i, 0 ) ; }

# for 100 random numbers between 0-9
for my $i ( map { int rand 10 } 1 .. 100 ) {
    $dis->{ $i }->() ;
    }
exit ;

# $m is the name
# $n is the count of times the program was called
# $o is zero-padded so we can sort 

sub maker ( $m, $n ) {
    return sub {
        $n++ ;
        my $o = sprintf '%04d', $n ;
        say join ' - ', $m, $o ;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们这样的输出:

```
5 - 0001
5 - 0002
8 - 0001
3 - 0001
6 - 0001
6 - 0002
2 - 0001
4 - 0001
3 - 0002
9 - 0001
...
9 - 0013
0 - 0010
9 - 0014
0 - 0011
6 - 0010
3 - 0008
6 - 0011
2 - 0006
8 - 0013
8 - 0014 
```

Enter fullscreen mode Exit fullscreen mode

但不完全是，因为随机。

我们看到`8`和`9`运行了 14 次，而`2`只运行了 6 次。

那很好，但不是我们所需要的。更像…

```
sub maker ( $key,$value ) {
    if ( defined $key && defined $value ) {
        return sub ( $obj ) {
            return 1 if defined $obj->{$key} && $obj->{$key} =~ m{$value}mix;
            return 0 ;
            }
        }
    return sub { return 1 } ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们给出一个键和一个值，例如`"track"`和`"community"`，如果 a)T2 在它接收的对象中，并且`"community"`是轨道名称的一部分，那么返回 1 的子程序。

```
my $dispatch ;
for my $k ( keys $config->%* ) {
    my $v = $config->{ $k } ;
    $dispatch->{ $k } = maker( $k, $v ) ;
    }
delete $dispatch->{json} if defined $dispatch->{json} ;

for my $k ( keys $dispatch->%* ) {
    $main->@* = grep { $dispatch->{ $k }->($_) } $main->@* ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个名为`$dispatch`的函数表，里面充满了子程序。`"track"`的那个内部知道它在找`"community"`，所以我们只需要`grep { $dispatch->{ track }->($_)}`来测试它。

比起它所取代的复制-粘贴、查找-替换代码，我更喜欢它。现在，在我忘记之前再用一次。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。