# 2018 年 Perl 大会让我做了什么

> 原文：<https://dev.to/jacoby/what-the-perl-conference-2018-made-me-do-2dje>

大卫·戈尔登做了一个关于[高阶 Perl](https://www.youtube.com/watch?v=USF4BHMFKbg&feature=youtu.be) 的演讲。高阶编程是编写返回函数的函数，这使得事情变得疯狂。(关于这一点，请参阅以前的文章。)这启发我把**高阶 Perl** 放回我的阅读清单上。

[Pudge](https://twitter.com/pudgenet) 受到启发回到 Perl 模块，给他的模块`[D'oh](https://metacpan.org/pod/D::oh)`增加了决定你想要的值的名字的能力，只需简单地输入`use D'oh wq{why_is_this_sucking} ;`

现在，正如我之前建立的，我从 [perlbrew](https://perlbrew.pl/) 偷了内核，它允许我接受一个命令，比如`perlbrew exec`或`perlbrew help`，然后寻找`&exec`和`&help`，我用`$s = $self->can($command) ; $self->$s if defined $s`做这个。

为了使`$self->can()`工作，它必须在应用程序的符号表中，因为我想要这个可移植的，我知道我需要 Perl 的[导出器](https://metacpan.org/pod/Exporter)的方法。我的魔法*这里* somes with `use Exporter qw{import}`，这里程序可以做`use Module qw{foo bar}`，只有模块`&foo`和`&bar`会出来玩。

在模块中，你有一个名为`@EXPORT`的变量，它允许你说`@EXPORT = qw{ foo bar }`，它发送`&foo`和`&bar`不管，所以程序只做`use Module`。(还有更多，但这就够了。)

我认为在两个地方维护一个名字列表——`@EXPORT qw{ foo }`和`sub foo {}`——有点……繁重。为什么要在两个地方做，当它可以是一个的时候？所以，我做了两件事:确保上面的`$command_`与`/^api_/`匹配，遍历模块的符号表(`%Module::`，当它与`/^api_/`匹配时推入`@EXPORT`。

这是我到达 SLC 之前的心态和代码。

在`@xdg`之前。在`@pudgenet`和`D'oh`之前。

金色让我想起了制造函数。当我计划我在 TPC 的日子时，我写了一个工具，它获取时间表 HTML 并将其解析为一个 hashrefs 数组，我将它保存到 JSON 中，然后我写了一个工具，它允许我搜索日期、时间、地点、演讲者，每一个都是彼此的复制和粘贴，这使得编写自动复制很容易。

这让我想到我可以得到`use DatabaseAccess qw{table1}`，它会给我一个生成的函数，这个函数在 pseudoperl 中给了我

```
 sub table1 {
        my $db = get_database_connection();
        my $query = 'SELECT * FROM table1';
        my $response = $db->arraryref($query, {controls=>{}});
            # controls means I get an array of hashrefs. I use it.
        return wantarray ? $response->@* : $response ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我记得在我玩 DBs 的早期，在使用表名的占位符时遇到了问题，所以我不相信`SELECT * FROM ?`会起作用，有了它，如果表存在并且我请求它，我就可以得到它。让我澄清一下，我仍然控制着用`use DatabaseAccess qw{ table1 }`放出什么，仅仅在别处请求`table7`是行不通的。(除非我让它这样。这是错误的。)

```
 sub tablemaker ( $table ) {
        return sub {
            my $db = get_database_connection();
            my $query = "SELECT * FROM $table" ;
            my $response = $db->arraryref($query, {controls=>{}});
            return wantarray ? $response->@* : $response ;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

但是我有一个匿名函数，可能是一个调度表，但是我不能把这些传递给主程序的符号表。

这又把我们带回了达奥。

D'oh 的*新*部分是，当你做`use D::oh qw{fmep}`的时候，它用自动加载创建`&fmep`。在这种情况下，它只做一件事:打印发送的任何数据的 JSON 编码。

```
sub AUTOLOAD {
    my $data ;
    if ( @_ > 1 ) {
        $data = \@_ ;
        }
    elsif ( ref $_[0] ) {
        $data = $_[0] ;
        }
    else {
        $data = [$_[ 0] ] ;
        }
    print STDERR encode_json( $data ), "\n" ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这只是忽略了所有的上下文，不管你要求什么，都只做一件事。在我的例子中，您希望使用`$AUTOLOAD`，命名调用者正在调用的函数。

```
# state $t2 makes it so $t2, our table-table, exists between uses
# our $AUTOLOAD gets what the caller called, like Module::function
# we pull the function name and make it the table name, $table
# we create the function if it doesn't exist
# then run it, returning the value

sub AUTOLOAD {
    state $t2;  
    our $AUTOLOAD ;
    my ( $table ) = reverse split m{::}, $AUTOLOAD ;
    $t2->{ $table } = tablemaker( $table ) unless defined $t2->{ table } ;
    return $t2->{ $table }->() ;
    }

# Not REALLY connectint to the database, because I'm writing this
# in a ballroom in another state.

sub tablemaker ($table) {
    return sub {
        my $query = "SELECT * FROM $table" ;
        return $query ;
        }
    }

jacob@Marvin MINGW64 ~/Dropbox/API
$ ./test.pl
SELECT * FROM foo
SELECT * FROM bar
SELECT * FROM blee
SELECT * FROM quuz 
```

Enter fullscreen mode Exit fullscreen mode

我并没有明确地把东西塞进`%Module::`，但是我**就是**让它像我一样运转，这就是我所需要的。对此我还想做更多的事情，但那将是负责任地添加功能，而不是鲁莽地滥用 Perl 的动态特性。我肯定会在某个时候把它作为演讲的一部分。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。