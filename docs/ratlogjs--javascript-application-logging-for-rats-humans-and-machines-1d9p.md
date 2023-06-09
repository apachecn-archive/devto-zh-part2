# ratlog . js–用于 rat、人类和机器的 JavaScript 应用程序日志

> 原文：<https://dev.to/jorinvo/ratlogjs--javascript-application-logging-for-rats-humans-and-machines-1d9p>

***每当我不得不查看 Node.js 项目中的日志时，我都不满足。
Ratlog 试图解决这个问题。***

登录 Node.js 的典型方式是使用[班扬](https://github.com/trentm/node-bunyan)或者[温斯顿](https://github.com/winstonjs/winston)。
这两个都是成熟的库，它们有很多选项:

您可以配置传输以将日志写入不同的位置，您可以灵活地指定输出格式，默认情况下包括许多数据点，如时间戳和日志级别。

他们使用 JSON 作为输出格式，以便灵活和兼容其他平台。他们还提供了简洁的 CLI 工具，用于在查看日志时漂亮地打印 JSON 输出。

但是所有这些灵活性使得使用日志很麻烦。

这些特性可能在很多情况下对很多人都有帮助。但是，如果您没有围绕日志记录的大型基础设施来集中和查询它们，这些特性会妨碍您。

我只想看看我的应用程序中发生了什么。

*   [stdout](https://en.wikipedia.org/wiki/Standard_streams) 是日志的正确位置。如果我想把它们放在别的地方，我可以重定向 stdout。
*   我不希望日志格式是可配置的。我希望它是一致的。
*   我不想使用另一个工具来读取日志。他们应该马上可读。JSON 不可读。
*   我甚至不想要日志级别和时间戳之类的东西。当我读日志时，主要是通过工具，比如`docker`或`journalctl`。他们已经收集了所有元信息，比如时间戳、主机信息、服务名，...

尽管这些库有很多特性，包括很多元信息，但它们并没有帮助我用工具来构建实际的日志。

* * *

所以我想到的是 [Ratlog](https://github.com/ratlog/ratlog-spec/) 。

Ratlog 是一种日志格式的规范，其重点在于可读性、信息性、简单性和机器可解析性。

此外，我创建了 [Ratlog.js](https://github.com/ratlog/ratlog.js) ，这是一个非常简单的 JavaScript 日志库，它支持 Ratlog 语义，使创建有用的日志变得简单。

* * *

让我们快速看一下一个基本的[示例应用](https://github.com/ratlog/ratlog.js/blob/master/examples/component-with-metrics.js) :
的一些输出

```
app starting
[counter] starting
[counter] started
app ready
[counter|event] counting | count: 1
[counter|event] counting | count: 2
[counter] stopped
app shutting down 
```

Enter fullscreen mode Exit fullscreen mode

读取服务的输出可能如下所示:

```
$ docker logs -t myapp
2018-03-29T11:10:29.116Z [file-import|warning] file not found | code: 404 | path: /tmp/notfound.txt 
```

Enter fullscreen mode Exit fullscreen mode

```
$ journalctl -u myapp
Apr 13 22:15:34 myhost myapp[1234]: [http|request|error] File not found | code: 404 | method: GET | route: /admin 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用所有默认的 Unix 工具来过滤、查询和操作输出:

```
$ journalctl -u myapp | grep '\[|\|warn' | less 
```

Enter fullscreen mode Exit fullscreen mode

日志由**消息**、**标签**和**字段**组成:

*   *消息*是日志最基本的元素。
*   可以使用*标签*将其放入上下文中。可以灵活地使用标记来表示日志级别、严重性、组件层次结构和许多其他属性。
*   此外，可以使用*字段*增加更多的数据。

* * *

如何开始记录？

*   安装[鼠夹](https://www.npmjs.com/package/ratlog) NPM 组件

```
npm i ratlog 
```

Enter fullscreen mode Exit fullscreen mode

*   开始记录

```
const log = require('ratlog')(process.stdout)

log('hello world')
// => hello world

// Add fields
log('counting', { count: 1 })
// => counting | count: 1

// Add fields and a tag
log('counting', { count: -1 }, 'negative')
// => [negative] counting | count: -1

// Create another logger bound to a tag
const warn = log.tag('warning')

warn('disk space low')
// => [warning] disk space low

// Combine and nest tags any way you like
const critical = warn.tag('critical')

critical('shutting down all servers')
// => [warning|critical] shutting down all servers 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 GitHub 上找到更多关于 [Ratlog.js](https://github.com/ratlog/ratlog.js) 和 [Ratlog Spec](https://github.com/ratlog/ratlog-spec) 的信息。

我真的很高兴听到你的想法，批评和反馈！

用任何其他编程语言实现 Ratlog 兼容的记录器或解析器也很简单。有一个 [JSON 测试套件](https://github.com/ratlog/ratlog-spec/blob/master/ratlog.testsuite.json)，所以你不必费心编写测试。