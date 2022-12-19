# 快速破解和高阶编程

> 原文：<https://dev.to/jacoby/quick-hacks-and-higher-order-programming-378p>

我正在盐湖城的 Perl 会议上谈论用户组。

或者说，领导一个小组。

无论如何，我的问题是“我的对手是谁？”而网站也没有让事情变得简单。

所以，作为一个程序员，我写了两个程序。第一个将时间表转换成 JSON 文档，另一个解析 JSON，给出您想要的表单。JSON、Perl 和 Getopt::Long 给了我一个快速而肮脏的方法，允许我限制听众、日期、时间、地点、演讲者和音轨:“显示 Regex 音轨上的中间对话”。

但是……

```
$main->@* = grep { defined $_->{ audience } && $_->{ audience } =~ m{$config->{audience}}mix } $main->@* if defined $config->{ audience } ;
$main->@* = grep { defined $_->{ date } && $_->{ date } =~ m{$config->{date}}mix } $main->@* if defined $config->{ date } ;
$main->@* = grep { defined $_->{ time } && $_->{ time } =~ m{$config->{time}}mix } $main->@* if defined $config->{ time } ;
$main->@* = grep { defined $_->{ location } && $_->{ location } =~ m{$config->{location}}mix } $main->@* if defined $config->{ location } ;
$main->@* = grep { defined $_->{ speaker } && $_->{ speaker } =~ m{$config->{speaker}}mix } $main->@* if defined $config->{ speaker } ;
$main->@* = grep { defined $_->{ track } && $_->{ track } =~ m{$config->{track}}mix } $main->@* if defined $config->{ track } ; 
```

Enter fullscreen mode Exit fullscreen mode

因为在做 JSON 的东西时，处理 arrayref 比处理数组容易，所以它是 array ref。为了简单起见，基本情况是这样的:

```
@mail = grep { defined $_->{ $keyword } && $_->{ $keyword } =~ m{$config->{$keyword}}mix } @main
    if defined $config->{$keyword} 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个很大的 hashrefs 数组，我们只想传递包含这个字段的条目，并且这个字段中的值就是我们想要的。

到目前为止，一切顺利。只不过，那是又大又丑的复制粘贴式编程。一定有更好的方法！！！他一边说，一边穿上了彩色的广告毛衣。

确实有。我不确定 1)我是否能成功地把它放进去，2)像这样的玩具项目(“我想查看活动日程，但我不想使用活动网站或电话应用程序！我想使用命令行，像一个*真正的极客*”)配得上这项工作。我的意思是，这是丑陋的，但它是这样做，它的工作。

(在写这篇文章的时候，我添加了一个`--json`标志，这样我就可以把它输入到`jq`中，因为我需要用它来练习。)

我不知道我是否应该把这作为更高的顺序或数据驱动，但在这种情况下，它们可能是同一事件的不同描述。我可以看到类似这样的东西

```
for my $keyword ( keys $config->%* ) {
    next if $keyworkd eq 'json' ; # because it isn't a filter
    @mail = grep { 
        defined $_->{ $keyword } && 
        $_->{ $keyword } =~ m{$config->{$keyword}}mix 
        } @main
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我添加描述，只需将它添加到`GetOptions`。实际上，*那个*会让我得到数据驱动，但是对于更高阶的，它可能更像这样:

```
 my $dispatch;
for my $keyword ( keys $config->%* ) {
    $dispatch->{$keyword} = sub ( $foo ) {
        return 1 if defined $foo->{$keyword} ;
        # and other such stuff -- I never go here so I really don't know
        # how to generate functions like this
        return 0 ;
    } ;
    }
# I am much more about hardcoding this mess

$dispatch->{json} = sub { return 1 }; 
```

Enter fullscreen mode Exit fullscreen mode

而且，在 TPC 之前的一周，我没有时间搞清楚这一步，之后，我就不会有紧迫感了。如果我完成了，我会成为一个更好的程序员，但是手头的问题解决了。我已经把高阶 Perl 从书架上拿走了，但是可能不会去读它。

哦好吧。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。