# 用 Lua 扩展 Nmap

> 原文：<https://dev.to/citizen428/extending-nmap-withlua-bpa>

这是我最初在 2013 年写的一篇相当旧的帖子，但它仍然是对 Lua 和 Nmap 脚本的一个很好的介绍，所以我决定重新发布它。

无论您是安全专家还是网络管理员，都有可能将 [Nmap](http://nmap.org/) (“网络映射器”)作为您常规工具包的一部分。对于许多人来说，这个由 Gordon“Fyodor”Lyon 于 1997 年启动的项目是主机和服务发现的首选。但由于 Nmap 脚本引擎(NSE)，它可以做得更多，允许用户轻松开发和共享自己的脚本，从而将其变成一个多功能的安全扫描器。

## 你将学到什么

*   Lua 编程语言的基础，
*   简单 NSE 脚本的演练，
*   在哪里可以找到关于编写 Nmap 和 Lua 脚本的更多信息。

## 你应该知道什么

*   基本 Nmap 用法，
*   命令式编程语言。

## Nmap 脚本引擎(NSE)

NSE 的核心是一个嵌入式 Lua 解释器。在 NSE 的实现开始之前，Nmap 开发人员考虑了各种编程语言，但最终选定了 Lua，因为它小巧、自由授权(MIT)、快速、积极开发，并且设计时考虑了可嵌入性。它也用于其他流行的自由/开源软件安全工具，如 Wireshark 和 T2 Snort，以及流行的项目，如魔兽世界和窗口管理器。

## Lua 编程语言

Lua 的发展始于 1993 年巴西里约热内卢的天主教大学。它是由罗伯托·伊鲁萨里姆施(Roberto Ierusalimschy)、瓦尔德马尔·塞蕾斯(Waldemar manuel)和路易斯·恩里克·德·菲格雷多(Luiz Henrique de Figueiredo)设计的，主要目标是轻量级、跨平台和易于嵌入其他项目。这些目标是通过用可移植的 ISO C 语言编写语言并给它一个相对简单的 C API 来实现的。此外，Lua 的解释器在编译后只占用大约 180 kB，所以嵌入它不会产生巨大的成本。多年来，它成为一种流行的脚本语言和扩展语言，广泛应用于各种项目中。任何有过 Modula 或 C 风格语言(控制结构，如 if、while、for)经验的人都应该熟悉 Lua 的语法，但设计者也大量借鉴了其他语言:Lua 的语义受 Scheme 的影响很大，尽管不同的语法使这一点有点模糊，但随着您使用该语言的时间越长，这一点就变得越明显。在某些方面，它感觉像是一个更干净、设计更好的 JavaScript，所以即使以前很少或没有接触过这种语言，通常也很容易理解给定的 Lua 代码片段。

## 关键特性

Lua 具有一级函数的特性，因此允许开发人员像对待任何其他数据类型一样对待函数。它也没有强加某种编程范式，而是根据需要提供元特性来扩展语言。例如，这使得在没有显式语言支持的情况下，以相当简单和直接的方式实现诸如继承之类的事情成为可能。Lua 也是垃圾收集的，以闭包和适当的 tails 调用为特色，并以协程的形式为协作式多任务处理提供了基础设施。虽然大部分内容超出了本文(和 NSE)的范围，但感兴趣的读者可以在优秀的书籍《Lua 编程》中找到更多信息，该书的旧版本可以在该项目的网站上免费获得。

## 表格

然而，Lua 还有一个特性值得更详细地讨论，即它依赖于单一的复合数据结构，即表。表本质上就是其他语言通常所说的关联数组、字典或键值对。这就是如何创建一个简单的表格:

```
table = { x = 10, y = "foo" } 
```

Enter fullscreen mode Exit fullscreen mode

可以这样访问值:`t[“x”]`和`t[“y”]`。对于字符串键，Lua 支持一种很好的简写方式，所以`t.x`和`t.y`会产生相同的结果。

Lua 表的一个特殊特性是，它们也被优化成数组，可以像这样创建:

```
array = { "hello", "lua", "world" } 
```

Enter fullscreen mode Exit fullscreen mode

索引是自动分配的，与其他语言不同，它从 1 开始，而不是从 0 开始(尽管可以显式分配给`array[0]`)。所以要从数组中取出字符串“lua ”,我们必须使用`array[2]`。

有了这个相对简单的数据结构(和强大的元表特性)，Lua 不仅实现了数组和字典，还实现了记录/结构、名称空间、类、对象等等。

## 例子

理论到此为止，让我们看一些 Lua 代码的例子。首先是惯常的“你好，世界”节目:

```
print “Hello world” 
```

Enter fullscreen mode Exit fullscreen mode

这将只是输出`Hello world`。现在为函数定义:

```
function sayHello(name)
  print ("Hello " .. name .. "!")
end 
```

Enter fullscreen mode Exit fullscreen mode

为了生成与上面相同的输出，我们必须调用带有适当参数的函数:`sayHello(“world”)`。或者，相同的函数可以这样定义:

```
sayHello = function(name)
  print ("Hello " .. name .. "!")
end 
```

Enter fullscreen mode Exit fullscreen mode

最后但并非最不重要的一个小循环，它遍历一个数组并打印出每个元素(`ipairs`返回数组的索引值对):

```
for i, str in ipairs(array) do
  print (i..": "..str)
end 
```

Enter fullscreen mode Exit fullscreen mode

假设变量数组包含元素“hello”、“lua”和“world”，这将生成以下输出:

```
1: hello
2: lua
3: world

```

## NSE 示例脚本

Nmap 网站包含一些关于 NSE 的优秀文档([简短介绍](https://nmap.org/book/man-nse.html)、[全书章节](https://nmap.org/book/nse.html)、[教程](https://nmap.org/book/nse-tutorial.html))，所以我们不重复那里写的所有内容，而是看一个实际的例子。这里显示的脚本是 [http_generator.nse](https://nmap.org/nsedoc/scripts/http-generator.html) ，它查找 http 生成器元标记的存在，以确定被扫描的主机运行哪个 CMS(如果有的话)。这些信息可能是有用的，因为这些系统的过时版本是常见的攻击媒介。

### 库导入

```
local http = require "http"
local shortport = require "shortport"
local stdnse = require "stdnse"
local string = require "string" 
```

Enter fullscreen mode Exit fullscreen mode

习惯上，通过导入所有需要的[库](http://nmap.org/book/nse-library.html)来启动 Nmap 脚本。在这个过程中，它们被分配给局部变量，所以如果你想用一个不同的(可能更短的)名字来访问它们，你可以这样做(例如`local s = require “string”`)。有各种各样的可用库，从协议(`afp`、`dns`、`http`、`bitcoin`、`openssl`等等。)过编码/解码(`base32`、`base64`、`json`)和实用程序库(`nmap`、`packet`、`pcre`)到一个针对服务的蛮力攻击的框架(`brute`)。我们正在查看的脚本使用`http`库进行通信，`shortport`用于更简单地定义端口规则(稍后将详细介绍)，而`stdnse`是由 NSE 提供的实用函数集合。`string`库是一个标准的 Lua 库，在本例中用于字符串匹配。

### 元数据

```
description = [[ Displays the contents of the "generator" meta tag of a web page(default: /) if there is one.
]]

author = "Michael Kohl"
license = "Same as Nmap--See http://nmap.org/book/man-legal.html"
categories = {"default", "discovery", "safe"}

---
-- @usage
-- nmap --script http-generator [--script-args http-generator.path=<path>,http-generator.redirects=<number>,...] <host>
--
-- @output
-- PORT    STATE SERVICE
-- 80/tcp  open  http
-- |_http-generator: TYPO3 4.2 CMS
-- 443/tcp open  https
-- |_http-generator: TYPO3 4.2 CMS
--
-- @args http-generator.path Specify the path you want to check for a generator meta tag (default to '/').
-- @args http-generator.redirects Specify the maximum number of redirects to follow (defaults to 3).

-- Changelog:
-- 2011-12-23 Michael Kohl <citizen428@gmail.com>:
--   + Initial version 
```

Enter fullscreen mode Exit fullscreen mode

在库导入之后，脚本包含一些用于文档和内部目的的元数据。`description`和`author`变量是不言自明的，前者使用的语法是 Lua 创建所谓的“heredocs”的方式，即保留空白和换行符的字符串文字。关于`license`脚本作者有两个选择，类似 GPL 的 Nmap 许可，以及简化的(2 条款)BSD 许可。最后但同样重要的是，有 NSEDoc 格式的文档。通常包括用法文档、示例输出、所有可用参数的描述(如果有的话)以及一个 changelog。

这个特定脚本没有使用另一个重要的字段，`dependencies`。这定义了脚本运行的顺序，用于一个脚本处理另一个脚本的结果的情况。重要的是要记住，这里列出的脚本不会自动运行，它们仍然需要通过`–script scriptname`或其他方式选择(例如`–script=default`)。

`categories`变量更有趣一点，它定义了我们当前的程序属于哪组脚本。当使用`-sC`或`-A`选项启动 Nmap 时，标记为`default`的脚本将自动运行。要符合这一类别，脚本必须遵守一些标准:它们需要快速、可靠、无阻碍且普遍有用。这些标准没有明确的定义，这取决于 Nmap 开发人员将脚本添加到此类别中。`discovery`类别收集所有主动尝试收集更多扫描目标信息的脚本，在我们的例子中是“生成器”HTML meta 标签。如果一个脚本被标记为`safe`，这意味着它不是为了崩溃服务或利用漏洞而编写的，也不太可能消耗大量资源。Nmap 使用更多类别，例如`auth`(处理认证凭证的脚本)`brute`(暴力攻击工具)或`vuln`(检查特定漏洞)。要获得完整的列表，请参考 Nmap 的优秀文档。

现在我们对类别有了更多的了解，让我们看看如何将这些知识用于更有趣的 Nmap 运行。让我们从运行所有默认脚本开始:

`nmap -sC example.com`

或者

`nmap --script=default example.com`

调用`nmap -A example.com`(“主动/高级扫描选项”)启用操作系统和版本检测、脚本扫描(相当于`nmap -sC`)和跟踪路由。

如果您只想运行一个脚本，可以通过指定`–script`参数来实现，例如`nmap –script http-generator example.com`。

通过使用类似于`nmap –script “default,safe” example.com`或`nmap –script “default or safe” example.com`的东西，也可以运行几个类别的脚本。有一个类似的`and`操作符，所以`nmap –script “default AND safe” example.com`将只运行两个列出类别中的脚本。

作为一个相当复杂的调用示例，以下命令将运行三个指定类别的所有脚本，但名称以“http-”开头的脚本除外:

`nmap --script "(default or safe or intrusive) and not http-*" example.com`

最后但同样重要的是，还可以运行不属于标准 Nmap 发行版的用户脚本，如下例所示:

`nmap --script default,banner,/home/user/customscripts example.com`

上面的调用将运行所有默认的脚本，名为“banner”的脚本和所有可以在目录`/home/user/customscripts`中找到的脚本。

### 规则

`rule`定义是每个 NSE 脚本的重要组成部分，它决定脚本何时运行。规则是一个 Lua 函数，它返回`true`或`false`来决定是否执行脚本的动作函数。有几种不同的规则类型，这取决于脚本应该何时运行(在扫描主机之前或之后，`prerule`和`postrule`)或者触发器是否是特定的主机或端口(`hostrule`和`portrule`)。

我们的示例脚本中的规则定义非常简单，因为它使用了非常方便的`shortport`库，该库有几个针对常见场景的预定义规则:

```
rule = shortport.http

```

当给定端口可能是 HTTP 端口时，这将匹配并返回 true。

### 动作

```
action = function(host, port)
    local path = stdnse.get_script_args('http-generator.path') or '/'
    local redirects = tonumber(stdnse.get_script_args('http-generator.redirects')) or 3

    -- Worst case: <meta name=Generator content="Microsoft Word 11">
    local pattern = '<meta name="?generator"? content="([^\"]*)" ?/?>'

    -- make pattern case-insensitive
    pattern = pattern:gsub("%a", function (c)
                  return string.format("[%s%s]", c, string.upper(c))
              end)

    local response = follow_redirects(host, port, path, redirects)
    if ( response and response.body ) then
        return response.body:match(pattern)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

`action`函数是每个 NSE 脚本的核心，它包含规则匹配时将执行的代码。基本上，`action`只是另一个 Lua 函数，它接受与`rule`函数相同的参数(由于使用了`shortport.http`规则，这在我们的例子中并不明显)。

该函数首先检查两个参数是否存在，并在用户没有为它们提供任何值的情况下指定默认值。可以通过以下方式将参数传递给脚本:

`nmap --script http-generator --script-args http-generator.path=/cms,http-generator.redirects=2 example.com`

它们存储在表`nmap.registry.args`中，但是你应该使用函数`stdnse.get_script_args`来访问它们，而不是直接使用它们。

在参数处理之后，指定一个匹配 HTTP“meta”标签的模式。标准 Lua 不包含正则表达式库，而是使用自己的模式。虽然有一个库为 Nmap 提供了 Perl 兼容的正则表达式(PCRE ),但是将它拖进单个表达式似乎有些过分，所以我依赖普通的 Lua 来实现这一点，这解释了下一行中使用的小变通方法，通过用小写和大写版本替换每个字母(模式`%a`)来使模式不区分大小写。之后，使用`follow_redirects`辅助函数来获取 HTTP 响应，如果有响应，它的主体将被扫描以获取我们的模式。

如果匹配，该函数将返回匹配的字符串，Nmap 将自动将其包含在输出中，如下所示:

```
STATE SERVICE
80/tcp open  http
| _http-generator: WordPress.com |

```

除了字符串，`action`还可以返回由名称-值对组成的表格，然后以结构化的方式对其进行格式化，而返回值`nil`根本不生成任何输出。

您可能已经注意到，我还没有向您展示上面提到的 helper 函数，因此尽管它本身并不十分有趣，但为了完整起见，我还是将它包括在内:

```
local follow_redirects = function(host, port, path, n)
    local loc
    local pattern = "^[hH][tT][tT][pP]/1.[01] 30[12]"
    local response = http.get(host, port, path)
    while (response['status-line'] or ""):match(pattern) and n > 0 do
        n = n - 1
        loc = response.header['location']
        response = http.get_url(loc)
    end
    return response
end 
```

Enter fullscreen mode Exit fullscreen mode

这个 helper 函数是跟踪 HTTP 重定向的简单方法。它首先定义了一个模式，用于在向指定的主机、端口和路径组合发出请求之前识别相关的 HTTP 状态代码(301 和 302)。然后，它检查是否存在重定向，如果存在重定向，就跟随它从新位置检索位置信息。当不再有重定向或者已经达到重定向限制(在参数`n`中指定)时，该函数将完整的 HTTP 响应返回给调用者。

## 深挖

这就是全部了！由于 NSE 及其所有库提供了出色的基础设施，编写定制的 Nmap 脚本是一项非常简单明了的任务。任何有一点脚本编写经验的人都应该能够在很短的时间内掌握基础知识，然后在开发脚本时了解更好的地方。Nmap 开发团队已经收集了一些优秀的文档，可以在[http://nmap.org/book/nse.html](http://nmap.org/book/nse.html.)在线找到。Fyodor 和 Nmap 的共同维护者 David Fifield [也在网上做了一个很棒的演示](https://nmap.org/presentations/BHDC10/)，让你在不到一个小时的时间里开始开发自己的 NSE 脚本。

## 总结

Nmap 是世界各地安全和网络专业人员的流行工具，其出色的脚本功能使它非常通用。Nmap 脚本引擎依赖于流行且易于学习的 Lua 编程语言，并提供有用的库来快速有效地开发您自己的定制脚本，然后可以与更广泛的 Nmap 用户社区共享。