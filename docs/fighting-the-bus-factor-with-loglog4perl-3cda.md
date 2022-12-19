# 用 Log::Log4Perl 对抗总线因素

> 原文：<https://dev.to/jacoby/fighting-the-bus-factor-with-loglog4perl-3cda>

[![A Bus](img/f23f28f5f6f07cddb337a92d992bc01a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---eMPktF0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jacoby.github.img/bus.jpg)【By little tung-Own work，CC BY-SA 3.0，】[https://en . Wikipedia . org/wiki/Big \ _ Blue \ _ Bus)https://commons . wikimedia . org/w/index . PHP？curid=26397084](https://en.wikipedia.org/wiki/Big%5C_Blue%5C_Bus)https://commons.wikimedia.org/w/index.php?curid=26397084)

听过短语[“公交因素”吗？](https://en.wikipedia.org/wiki/Bus_factor)简而言之，它关系到如果你的组织中有人“被公交车撞了”会发生什么。不一定是字面意思；换个工作就够了。

一个同事在这里兼职，在组织的其他地方兼职。然后，他可以偶尔提供咨询。他的代码现在在我的腿上。大部分是狂欢，以他理解的方式组织，但我肯定不理解。不明白它是怎么工作的，怎么叫的。

我**是否**了解 Perl，以及 Log4perl 的强大功能。

Log::Log4perl 是 perl 对 Log4J 的继承，有很多*选项。*

我写了一个快捷的东西，可以记录你放在`@ARGV`里的任何东西。

```
#!/usr/bin/env perl

use strict;
use warnings;
use utf8;
use feature qw{ postderef say signatures state };
no warnings qw{ experimental::postderef experimental::signatures };

use Log::Log4perl;

my $config = join '/', $ENV{HOME}, '.log.conf';
my $argv = join ' ', @ARGV;

Log::Log4perl::init($config);
my $logger = Log::Log4perl::get_logger('log_me');
$logger->warn($argv); 
```

Enter fullscreen mode Exit fullscreen mode

Log::Log4perl 不在内核中，但是它非常有用。

所以，现在，如果我运行`log_me.pl test`，我得到

```
2018/08/17 11:06:23 WARN localhost.example.com: test 
```

Enter fullscreen mode Exit fullscreen mode

因为大部分的魔力都在配置中。

```
log4perl.logger.log_me = TRACE, Appender0
log4perl.appender.Appender0 = Log::Log4perl::Appender::File
log4perl.appender.Appender0.filename = /home/jacoby/.log_me.log
log4perl.appender.Appender0.layout = PatternLayout
log4perl.appender.Appender0.layout.ConversionPattern=%d %p %H: %m%n

## log4perl.logger.log_me = TRACE, Appender0
## TRACE is the minimum level that will be logged
## (FATAL,ERROR,WARN,INFO,DEBUG)

## log4perl.appender.Appender0 = Log::Log4perl::Appender::File
## Appender0 is the ID of the appender we're using
## Screen, File, Socket, DBI, RRDs
## We append to file so it can occur without watching

## log4perl.appender.Appender0.filename = /depot/gcore/apps/dev/dave/.log_me.log
## filename is the name of the logfile

## log4perl.appender.Appender0.layout = PatternLayout
## log4perl.appender.Appender0.layout.ConversionPattern=%d %p %H: %m%n
## Date time LogLevel Host: the log test \n
## 2018/08/17 10:59:39-WARN-localhost.example.com-AGAIN 
```

Enter fullscreen mode Exit fullscreen mode

我记录了上面五行的意思。据推测，我可以添加更复杂的行为，但我需要的是简单的。我需要知道叫什么，多久一次，在哪里。

```
# Logging usage for triage in understanding and refactoring - DAJ 201808
me="$(basename "$(test -L "$0" && readlink "$0" || echo "$0")")"
/home/jacoby/log_me.pl $me 
```

Enter fullscreen mode Exit fullscreen mode

当符号链接时,`me`行得到正确的文件名(根据[这个关于栈溢出的回答](https://stackoverflow.com/questions/192319/how-do-i-know-the-script-file-name-in-a-bash-script)；嘿，我的 bash-fu 并不强大)，我已经将它粘贴到了所讨论的目录中的每个 shell 脚本的顶部。

```
2018/08/17 13:20:13 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 13:30:22 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 13:30:22 WARN localhost.example.com: runs_status.sh
2018/08/17 13:30:22 WARN localhost.example.com: runs_status.sh
2018/08/17 13:40:12 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 13:45:19 WARN localhost.example.com: user_warning.sh
2018/08/17 13:50:15 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 14:00:26 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 14:10:12 WARN localhost.example.com: should_blast_be_queued.sh
2018/08/17 14:20:13 WARN localhost.example.com: should_blast_be_queued.sh 
```

Enter fullscreen mode Exit fullscreen mode

这里的要点是分类:最常运行的程序是我应该最了解的程序。看起来`should_blast_be_queued.sh`是目前优先级最高的。

就像在十字路口向两边看一样。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。