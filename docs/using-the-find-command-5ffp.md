# 使用查找命令

> 原文：<https://dev.to/nicholascloud/using-the-find-command-5ffp>

`find`命令用于递归定位目录层次中的文件。由于程序员和系统管理员花费大量的时间处理文件，所以熟悉这个命令可以提高终端的效率。

该命令由四部分组成:

*   命令名
*   控制命令如何搜索的选项(可选)
*   要搜索的路径(必需)
*   表达式(由按名称过滤文件的“主”和“运算符”组成(可选)

> 初级是一个开关，如`-name`或`-regex`，它可能有也可能没有附加参数。运算符是以逻辑方式组合表达式的主运算符，如`-or`和`-not`。

# 基本用法

按名称查找文件可能是`find`命令最常见的用法。它的输出包含文件名在其搜索的目录结构中出现的所有路径。

```
$ find . -name README.md
./node_modules/hexo-renderer-stylus/README.md
./node_modules/is-extendable/README.md
./node_modules/striptags/README.md
...
./README.md 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:所有终端示例都是在我的博客的目录结构中生成的，由 [Hexo](https://hexo.io/) 静态站点生成器创建。

第一个参数中给`find`的路径是每个结果的前置路径。`.`路径指示`find`在工作目录下搜索，并在输出中生成相对路径。但是，如果使用工作目录的绝对路径，完整路径将出现在结果中。命令替换可用于在简洁和更详细的输出之间取得折衷。

```
$ find `pwd` -name README.md
/Users/nicholascloud/projects/nicholascloud.com/node_modules/hexo-renderer-stylus/README.md
/Users/nicholascloud/projects/nicholascloud.com/node_modules/is-extendable/README.md
/Users/nicholascloud/projects/nicholascloud.com/node_modules/striptags/README.md
...
/Users/nicholascloud/projects/nicholascloud.com/README.md 
```

Enter fullscreen mode Exit fullscreen mode

如果省略过滤表达式，`find`将返回*其搜索范围内的所有文件路径*。

```
$ find .
.
./scaffolds
./scaffolds/draft.md
./scaffolds/post.md
./scaffolds/page.md
... 
```

Enter fullscreen mode Exit fullscreen mode

# 其他场景

然而,`find`命令能做的不仅仅是通过精确的名字定位文件。它可以在许多不同的场景中根据各种标准查找文件。

## 我们可能不知道我们正在搜索的文件的确切名称

`-name` primary 支持通配符搜索。

*   星号(`*`)可以替换任何连续的字符数
*   问号(`?`)可以替换任何单个字符

```
$ find . -name '*javascript*.md'
./source/_posts/javascript-frameworks-for-modern-web-dev.md
./source/_posts/l33t-literals-in-javascript.md
./source/_posts/historical-javascript-objects.md
./source/_posts/maintainable-javascript-book-review.md 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，`-name` primary 是区分大小写的。要进行区分大小写的搜索，我们可以使用`-iname` primary。

```
$ find . -iname '*JAVA*.md'
./source/_posts/java-4-ever.md
./source/_posts/javascript-frameworks-for-modern-web-dev.md
./source/_posts/l33t-literals-in-javascript.md
./source/_posts/historical-javascript-objects.md
./source/_posts/maintainable-javascript-book-review.md 
```

Enter fullscreen mode Exit fullscreen mode

对于更复杂的搜索，我们可以使用带有`-regex`和`-iregex`(不区分大小写)主键的正则表达式。

> **注意:**使用`-E`选项指定使用*扩展*正则表达式，而不是*基本*正则表达式。

```
 $ find -E . -regex '.*package(-lock)?\.json'
./node_modules/hexo-renderer-stylus/package.json
./node_modules/is-extendable/package.json
./node_modules/striptags/package.json
./node_modules/babylon/package.json
...
./package-lock.json
./package.json 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可能希望将我们的结果限制到特定的路径，或者匹配多个路径的模式

为了通过路径掩码过滤结果，我们可以用`-path`和`-ipath`指定一个模式(不区分大小写)。两者都支持星号和问号通配符。

```
$ find . -path './node_modules/*/lib/*' -regex '.*hexo.*'
./node_modules/hexo-renderer-stylus/lib/renderer.js
./node_modules/hexo-renderer-marked/lib/renderer.js
./node_modules/hexo-generator-archive/lib/generator.js
./node_modules/hexo-migrator-wordpress/node_modules/async/lib/async.js
./node_modules/hexo-log/lib/log.js
./node_modules/hexo-generator-category/lib/generator.js
./node_modules/hexo-i18n/lib/i18n.js
./node_modules/hexo-pagination/lib/pagination.js
./node_modules/hexo-generator-index/lib/generator.js
./node_modules/hexo-util/lib/pattern.js 
```

Enter fullscreen mode Exit fullscreen mode

使用`-path`主目录不会改变`find`开始搜索的顶级目录；它只是通过子目录过滤结果。

## 我们可能想要关于我们找到的文件的详细信息

为了查看关于文件的详细信息，以类似于 [`ls -l`](http://man7.org/linux/man-pages/man1/ls.1.html) 的方式，可以将`-ls`主文件附加到主文件列表中。

```
$ find . -name *.md -path '*/_posts/*' -ls
8636130637  8 -rw-r--r--  1 nicholascloud  staff  490 Oct 24 12:25 ./source/_posts/strange-loop-2010-video-release-schedule-posted.md
8637286945  8 -rw-r--r--  1 nicholascloud  staff  1570 Oct 24 12:45 ./source/_posts/god-mode-in-windows-7-not-as-cool-as-rise-of-the-triad.md
8636130716  8 -rw-r--r--  1 nicholascloud  staff  204 Oct 24 12:25 ./source/_posts/what-writing-fiction-taught-me-about-writing-software.md
... 
```

Enter fullscreen mode Exit fullscreen mode

(作为`-ls`主命令的替代，`-exec`主命令可用于调用 [`ls`](http://man7.org/linux/man-pages/man1/ls.1.html) ，或者 [`xargs`](http://man7.org/linux/man-pages/man1/xargs.1.html) 命令可用于相同目的。)

## 一旦我们找到了要搜索的文件，我们可能想要停止向下进入一个层次结构

一旦找到与表达式匹配的结果，`-prune` primary 就会使`find`停止遍历特定的目录路径。然而，它将继续在同一目录层搜索*作为其他潜在匹配的结果。* 

```
 $ find . -regex '.*middleware.*' -prune
./source/_posts/new-appendto-blog-post-streams-and-middleware-in-strata-js.md
./node_modules/stylus/lib/middleware.js
./node_modules/hexo-server/lib/middlewares
./public/2013/06/new-appendto-blog-post-streams-and-middleware-in-strata-js
./.deploy_git/2013/06/new-appendto-blog-post-streams-and-middleware-in-strata-js 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`diff`工具和 IO 重定向，我们可以将“修剪”后的结果集的输出与未修剪的结果集的输出进行比较，以查看哪些路径被省略了。例如，在下面的 diff 中，一旦`/node_modules/hexo-server/lib/middlewares`被添加到结果集中，匹配`/node_modules/hexo-server/lib/middlewares/*`的剩余路径就会被忽略。

```
$ diff <(find . -regex '.*middleware.*') <(find . -regex '.*middleware.*' -prune)
4,9d3
< ./node_modules/hexo-server/lib/middlewares/route.js
< ./node_modules/hexo-server/lib/middlewares/redirect.js
< ./node_modules/hexo-server/lib/middlewares/logger.js
< ./node_modules/hexo-server/lib/middlewares/gzip.js
< ./node_modules/hexo-server/lib/middlewares/header.js
< ./node_modules/hexo-server/lib/middlewares/static.js
11d4
< ./public/2013/06/new-appendto-blog-post-streams-and-middleware-in-strata-js/index.html
13d5
< ./.deploy_git/2013/06/new-appendto-blog-post-streams-and-middleware-in-strata-js/index.html 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可能只想搜索*到*特定深度或者搜索特定深度以外的

 *几个主要元素控制深度遍历，或者说`find`将走多远来定位结果。

`-maxdepth`控制 `find`在停止前将要经过的路径深度*。* 

```
$ find . -name *.css -maxdepth 3
./public/fancybox/jquery.fancybox.css
./public/css/style.css
./.deploy_git/fancybox/jquery.fancybox.css
./.deploy_git/css/style.css 
```

Enter fullscreen mode Exit fullscreen mode

`-mindepth`控制 `find`开始搜索的路径深度*。* 

```
$ find . -name *.css -mindepth 6
./node_modules/hexo/node_modules/hexo-cli/assets/themes/landscape/source/fancybox/jquery.fancybox.css
./node_modules/hexo/node_modules/hexo-cli/assets/themes/landscape/source/fancybox/helpers/jquery.fancybox-thumbs.css
./node_modules/hexo/node_modules/hexo-cli/assets/themes/landscape/source/fancybox/helpers/jquery.fancybox-buttons.css
./themes/landscape/source/fancybox/helpers/jquery.fancybox-thumbs.css
./themes/landscape/source/fancybox/helpers/jquery.fancybox-buttons.css 
```

Enter fullscreen mode Exit fullscreen mode

`-depth`指定`find`将搜索的*精确*深度。

```
$ find . -name *.css -depth 5
./node_modules/async-limiter/coverage/lcov-report/prettify.css
./node_modules/async-limiter/coverage/lcov-report/base.css
./themes/landscape/source/fancybox/jquery.fancybox.css 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可能希望找到相对于另一个文件较新/较旧的文件

`-newer`主文件将通过比较每个文件的修改时间来查找比指定文件更新的文件。

```
$ ls -l source/_drafts/
-rw-r--r--  1 nicholascloud  staff  189 Nov    7 11:51:20 2018 the-importance-of-names.md
-rw-r--r--  1 nicholascloud  staff  353 Nov    7 11:50:49 2018 the-most-satisfying-thing.md
-rw-r--r--  1 nicholascloud  staff  10812 Nov  8 19:13:09 2018 using-the-find-command.md

$ find . -newer source/_drafts/the-importance-of-names.md -path '*_drafts*'
./source/_drafts/using-the-find-command.md 
```

Enter fullscreen mode Exit fullscreen mode

为了更细粒度的控制，使用`-newer[XY]`主文件，其中`X`和`Y`的值代表不同种类的文件时间戳(见下表)。`X`的时间戳适用于`find`评估的文件；`Y`适用于提供用于比较的文件路径参数

| `X` / `Y`旗帜 | 价值 |
| --- | --- |
| `a` | 存取时间 |
| `B` | 信息节点创建时间 |
| `c` | 更改时间(文件属性) |
| `m` | 修改时间(文件内容) |
| `t`(仅`y`) | `file`被解释为`cvs(1)`理解的日期 |

例如，命令`find . -neweram foo.txt`将找到所有具有比`foo.txt`的*修改时间*更新的*访问时间*的文件。

对于每个`X`标志，都有快捷方式主标志，用于与文件参数的修改时间进行比较。

*   `-anewer`将结果集中每个文件的访问时间与指定文件的修改时间进行比较。
*   `-bnewer`将结果集中每个文件的信息节点创建时间与指定文件的修改时间进行比较。
*   `-cnewer`将结果集中每个文件的更改时间与指定文件的修改时间进行比较。
*   `-mnewer`将结果集中每个文件的修改时间与指定文件的修改时间进行比较，与`-newer`相同。

## 我们可能想搜索一种特定的“文件”(文件、目录、符号链接等。)

在类 Unix 系统中，[“一切都是文件”](https://en.wikipedia.org/wiki/Everything_is_a_file)，这些文件都有类型。`find`命令可以检测文件类型，并相应地过滤结果。常规文件(我们最常搜索的)的类型是`f`；目录有一种类型的`d`。块文件(例如磁盘)的类型是`b`。

在 OSX，很容易找到代表磁盘(物理和逻辑)的所有块文件。

```
$ find /dev -name 'disk*' -type b
/dev/disk0
/dev/disk0s1
/dev/disk0s2
/dev/disk1
/dev/disk1s2
/dev/disk1s3
/dev/disk1s1
/dev/disk1s4 
```

Enter fullscreen mode Exit fullscreen mode

下表列出了`find`命令可能检测到的每种文件类型。

| 旗 | 意义 |
| --- | --- |
| `b` | 街区特辑 |
| `c` | 特殊字符 |
| `d` | 目录 |
| `f` | 常规文件 |
| `l` | 符号链接 |
| `p` | 先进先出。比较 LIFO |
| `s` | (电源)插座 |

## 我们可能想要搜索特定用户或组拥有的文件(或者相反，不属于已知用户或组的文件)

在类似 Unix 的系统中，用户和组通过名称和数字 ID 来标识。在 OSX，`id`命令告诉我我的用户 ID 和组 ID。

```
$ id
uid=501(nicholascloud) gid=20(staff)... 
```

Enter fullscreen mode Exit fullscreen mode

`find`命令接受按用户和/或组过滤文件结果的初选。

*   `-uid <uid>`和`-user <username>`按所属用户过滤结果。如果`-user`的参数是数字，并且不存在具有该名称的组，则假定它是一个用户 ID。
*   `-gid <id>`和`-group <groupname>`按所属组过滤结果。同样的警告也适用于`groupname`和`username`。

我为一个名为 OwlEyes.org 的网站编写代码，这是一个由 apache2 web 服务器提供服务的 PHP 应用程序。如果我在属于`www-data`用户(典型的 apache2 用户)的主目录中搜索文件，我会看到一些有趣的结果。

```
$ find . -user www-data
./projects/enotes/owleyesorg/app/logs/apache-error.log
./projects/enotes/owleyesorg/app/logs/apache-custom.log 
```

Enter fullscreen mode Exit fullscreen mode

我的项目目录中的所有其他文件都属于我的用户，但是 apache2 日志文件是由 web 服务器编写的，因此属于它的用户。

为了找到不属于任何已知用户和/或组的文件，可以使用逆原色。

*   `-nouser <username>`显示*不属于*已知用户的结果。
*   `-nogroup <groupname>`显示*不属于*已知组的结果。

## 我们可能想要找到空的文件或目录

要查找空文件(0 字节文件或没有文件的目录),将`-empty`主文件附加到`find`命令。例如，这对于查看系统中哪些日志文件是空的非常有用。

```
$ sudo find /var/log -empty
./appfirewall.log
./ppp
./alf.log
./apache2
./com.apple.xpc.launchd
./cups
./CoreDuet
./uucp 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可能想要对由`find`标识的文件运行一个实用程序

`-exec`和`-ok`主要用于在`find`的结果集中的每个文件上运行一个命令。这两个主要文件是相同的，但是`-ok`在执行指定命令之前会要求用户对每个文件进行确认。

用`find`执行命令的语法是:

```
find <expression(s)> -exec <command> \; 
```

Enter fullscreen mode Exit fullscreen mode

该命令是以标准格式编写的，就像您在终端中键入它一样。如果字符串`'{}'`出现在命令中的任何地方，当 find 遍历这些结果时，它将被每个结果的文件路径替换。命令*必须由`\;`终止*。(在 shell 环境中执行时，转义字符是必需的。)

命令`find . -newer db.json -type f -exec cp '{}' ~/tmp \;`:

*   从当前目录开始
*   查找在`db.json`之后修改的文件(存储博客文章信息的数据库文件)
*   查找“常规文件”类型的文件
*   并将每个文件复制到`~/tmp`

```
$ ls -lh db.json
-rw-r--r--  1 nicholascloud  staff   2.6M Nov  8 19:15 db.json

$ find . -newer db.json -type f -exec cp '{}' ~/tmp \;

$ ls -l ~/tmp
-rw-r--r--   1 nicholascloud  staff    61B Nov  9 10:46 README.md
-rw-r--r--   1 nicholascloud  staff    14K Nov  9 10:46 using-the-find-command.md 
```

Enter fullscreen mode Exit fullscreen mode

两个相应的初选，`-execdir`和`-okdir`做的事情与`-exec`和`-ok`相同，但是`'{}'`被替换为*尽可能多的来自结果集*的文件路径，使得这些初选类似于 [`xargs`](http://man7.org/linux/man-pages/man1/xargs.1.html) 。例如，为了将文件归档到一个`find`结果集中，可以使用`-execdir`创建一个 tarball。

```
$ find . -newer db.json -type f -execdir tar cvzf ~/tmp/back.tar.gz '{}' \;
a using-the-find-command.md
a README.md 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可能想要格式化`find`的输出

`find`的输出有两种格式。

通过指定`-print` primary，在`find`的结果集中每个结果的文件路径被打印到标准输出，由一个新行结束。这是默认情况下`find`显示结果的方式。然而，一些主服务器，如`-exec`，可能*而不是*将每个文件打印到终端。命令`find . -newer db.json -type f -print -exec cp '{}' ~/tmp \;`将把所有比`db.json`新的文件复制到`~/tmp`，但是输出将保持为空(`cp`命令的默认行为)。为了强制显示每个文件，可以在`-exec`之前添加`-print`主文件。

```
$ find . -newer db.json -type f -print -exec cp '{}' ~/tmp \;
./source/_drafts/using-the-find-command.md
./README.md 
```

Enter fullscreen mode Exit fullscreen mode

`-print0` primary 创建了一个由空格分隔的所有文件路径的字符串，当将`find`的输出通过管道传输到 [`xargs`](http://man7.org/linux/man-pages/man1/xargs.1.html) 或其他类似的命令时，它会非常有用，因为这些命令需要这种格式的输入。

## 我们可能想要组合表达式来改进我们的搜索

默认情况下，原色被组合在一起应用以形成一个表达式，但是`find`支持两个改变表达式应用方式的操作符。如果两个表达式被`-or`操作符分开，那么它们将以布尔`OR`的方式应用；将返回匹配任一表达式或两者的结果。

```
$ find . -name '*eclipse*' -or -name '*clean*'
./sourimg/2011/10/eclipse-example.png
./sourimg/2011/10/eclipse-example-150x127.png
...
./sourimg/2011/08/clean-coders1.png
./sourimg/2011/08/clean-coders-150x117.png
... 
```

Enter fullscreen mode Exit fullscreen mode

如果`-not`(或`!`)操作符在一个表达式之前，它将取反该表达式，并从结果集中删除匹配的文件路径。

```
$ find . -name '*eclipse*'
./sourimg/2011/10/eclipse-example.png
./sourimg/2011/10/eclipse-example-150x127.png
./sourimg/2011/10/eclipse-example-2-300x97.png
./sourimg/2011/10/eclipse-example-2.png

$ find -E . -name '*eclipse*' ! -regex '.*[0-9]+x[0-9]+.*'
./sourimg/2011/10/eclipse-example.png
./sourimg/2011/10/eclipse-example-2.png 
```

Enter fullscreen mode Exit fullscreen mode

(回想一下，上例中的`-E`选项强制`find`在评估`-regex`主函数时使用扩展正则表达式。)

## 我们可能想删除找到的文件

虽然可以使用`-execdir rm '{}' \;`来删除结果集中的文件，但是`find`支持一个更短的主服务器，`-delete`可以完成相同的任务。默认情况下，`-delete`不会显示每个被删除文件的输出；将`-print`主菜单与`-delete`结合使用，查看哪些文件已从文件系统中删除。*