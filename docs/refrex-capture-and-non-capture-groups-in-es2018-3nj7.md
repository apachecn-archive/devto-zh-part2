# Refrex！ES2018 中的采集组和非采集组

> 原文：<https://dev.to/themindfuldev/refrex-capture-and-non-capture-groups-in-es2018-3nj7>

我最近对 Javascript 中的 regex 有了一些兴趣，并学到了一些有用的东西，我想与大家分享，所以我开始了一个名为 **Refrex** 的系列，这是对 Regex 的复习。

[![Ba Dum Tss](../Images/a029622b6aab8bfbfb72de80a5128a03.png)T2】](https://i.giphy.com/media/XEdeYqzH36e5O/giphy.gif)

那还不算太糟，是吗？

无论如何，让我们从一些基础开始，然后分成捕获组和非捕获组。

这是我手头的任务:我需要提取特定域的完整主机、子域和 URL 路径(为了这个练习，我将使用`google.com`)。

它必须适用于以下所有可能性:

```
www.google.com/search?q=devto
//www.google.com
http://www.google.com
https://www.google.com/search?q=devto
mail.google.com
something.like.google.com/for-me 
```

Enter fullscreen mode Exit fullscreen mode

我对正则表达式有点生疏，但我记得一些事情:

*   正则表达式由两个`/`分隔。
*   在第一个和第二个`/`之间，我们放置正则表达式模式，在第二个`/`之后，我们放置标志，例如代表区分大小写的`i`或代表全局的`g`。
*   有字符类如`.`代表除换行符以外的所有字符。
*   当我们想在模式中使用某些字符时，我们必须对它们进行转义，比如`.`和`/`，这样它们就不会与字符类和正则表达式分隔符混淆。然后我们需要使用`\.`和`\/`。
*   量词有`?` (0 次或 1 次)、`*` (0 次或多次)、`+` (1 次或多次)。
*   我们可以在单个字符后使用量词。
*   我们也可以用括号将字符分组，这样我们就可以在整个组中使用量词。
*   当我们使用括号组成组时，在使用字符串的`match`函数时，它们也会被捕获并分别返回。

所以我是这样开始的:

```
const regex = /((https?:)?\/\/)?(.*)\.google\.com(.*)/i; 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它分成几个部分。首先，协议:

1.  `https?` -这将捕获`http`或`https`。注意字母“s”在这里是可选的，因为我们使用的是`s?`。
2.  `(https?:)?` -这将捕获`http:`、`https:`或者什么都不捕获，因为整个组是可选的。
3.  `((https?:)?\/\/)?` -这将捕获带斜线的协议，可以是`http://`、`https://`、`//`或无，因为整个组是可选的。

现在，对于其余的:

1.  `(.*)` -这将捕获子域
2.  `\.google\.com` -这将匹配`.google.com`
3.  `(.*)` -这将捕获路径

因此，当我们对这些 URL 使用带有正则表达式的`match`时，我们得到的是:

```
'https://www.google.com/search?q=devto'.match(regex);

// Result:
{
  0: "https://www.google.com/search?q=devto",
  1: "https://",
  2: "https:",
  3: "www",
  4: "/search?q=devto",
  groups: undefined,
  index: 0,
  input: "https://www.google.com/search?q=devto",
  length: 5
} 
```

Enter fullscreen mode Exit fullscreen mode

在返回的对象中，元素 0 是完全匹配的结果，每个后续的数字元素按顺序对应于 regex 中出现的每个捕获组。

到目前为止，我可以看到，通过组合 group 1 + `.google.com` + group 4，它包含 group 3 的子域、group 4 的路径和完整的主机，所以现在我可以编写一个类似于
的函数

```
function extract(url) {
  const regex = /((https?:)?\/\/)?(.*)\.google\.com(.*)/i;

  const match = url.match(regex);

  let result = {};
  if (match !== null) {
    result = {
      host: `${match[1]}${match[3]}.google.com`,
      subdomain: match[3],
      path: match[4]
    };
  }

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，这样使用:

```
extract('https://www.google.com/search?q=devto');

// Result:
{
  host: "https://www.google.com",
  path: "/search?q=devto",
  subdomain: "www"
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，这个[好像没多干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。看起来我应该能够直接从正则表达式中获得完整的主机，而不需要对结果做任何连接，也不需要对部分`.google.com`进行硬编码。

所以我继续添加了另一个组来捕获完整的主机:

```
const regex = /(((https?:)?\/\/)?(.*)\.google\.com)(.*)/i;

'https://www.google.com/search?q=devto'.match(regex);

// Result:
{
  0: "https://www.google.com/search?q=devto",
  1: "https://www.google.com",
  2: "https://",
  3: "https:",
  4: "www",
  5: "/search?q=devto",
  groups: undefined,
  index: 0,
  input: "https://www.google.com/search?q=devto",
  length: 6
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！所以现在我们把完整的主机作为组 1，子域作为组 4，路径作为组 5，我的函数现在可以简化为:

```
function extract(url) {
  const regex = /(((https?:)?\/\/)?(.*)\.google\.com)(.*)/i;

  const match = url.match(regex);

  let result = {};
  if (match !== null) {
    result = {
      host: match[1],
      subdomain: match[4],
      path: match[5]
    };
  }

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利！然而，仔细看，我真的不需要组 2 和组 3 做任何事情。它们之所以存在，是因为我们想在它们周围使用量词，但我们对单独捕获它们的值一点也不感兴趣。这是因为默认情况下所有组都会被捕获。

然后，我在 dev.to 上做了一些研究，发现了这篇不错的文章:

[![stefanjudis image](../Images/f37d9b8b4ea61d5bf15857718cd3b186.png)](/stefanjudis) [## TIL:正则表达式中的非捕获组

### 斯特凡·朱迪斯 5 月 16 日 182 分钟阅读

#javascript](/stefanjudis/til-non-capturing-groups-in-regular-expressions-2p7h)

现在我知道了我可以把任何一个组变成一个非捕获组，只要在它前面加上`?:`！谢谢， [@stefanjudis](https://dev.to/stefanjudis) ！

现在我们开始，使第 2 组和第 3 组不被捕获:

```
const regex = /((?:(?:https?:)?\/\/)?(.*)\.google\.com)(.*)/i;

'https://www.google.com/search?q=devto'.match(regex);

// Result:
{
  0: "https://www.google.com/search?q=devto",
  1: "https://www.google.com",
  2: "www",
  3: "/search?q=devto",
  groups: undefined,
  index: 0,
  input: "https://www.google.com/search?q=devto",
  length: 4
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。现在，我们将组 1 作为完整主机，组 2 作为子域，组 3 作为路径。我们可以相应地重写函数`extract`。

然而，就在这之前，让我们去锦上添花吧！ES2018 引入了[命名的捕获组](https://mathiasbynens.be/notes/es-regexp-proposals#named-capture-groups)，这意味着每个组现在都可以通过名称来引用。语法是在每个组的开头加上`?<name>`。

因此，现在我们的正则表达式将如下运行:

```
const regex = /(?<host>(?:(?:https?:)?\/\/)?(?<subdomain>.*)\.google\.com)(?<path>.*)/i;

'https://www.google.com/search?q=devto'.match(regex);

// Result:
{
  0: "https://www.google.com/search?q=devto",
  1: "https://www.google.com",
  2: "www",
  3: "/search?q=devto",
  groups: {
    host: "https://www.google.com",
    path: "/search?q=devto",
    subdomain: "www"
  },
  index: 0,
  input: "https://www.google.com/search?q=devto",
  length: 4
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的`extract`函数变得简单了一点:

```
function extract(url) {
  const regex = /(?<host>(?:(?:https?:)?\/\/)?(?<subdomain>.*)\.google\.com)(?<path>.*)/i;

  const match = url.match(regex);

  return match !== null? match.groups: {};
} 
```

Enter fullscreen mode Exit fullscreen mode

> **免责声明**:这里“更简单”的概念可以是主观的。通常，在正则表达式的复杂性和代码的复杂性之间有一个折衷。在这种情况下，我们编写一个更复杂的正则表达式来支持不太复杂的代码。
> 
> 长话短说，我的建议是，所以你不要把这种方式看得太重，用你最好的判断，永远不要走得太极端。记住:你为人类编写代码，即使你不在了，人类也必须维护它。因此，如果您用有史以来最复杂的正则表达式编写了最优雅的代码，这并没有多大帮助。

你可以用命名的捕获组做更多的事情:在[反向引用](https://github.com/tc39/proposal-regexp-named-groups#backreferences)和[替换目标](https://github.com/tc39/proposal-regexp-named-groups#replacement-targets)中使用它们。

反向引用完全是另一个问题，我计划在下一篇文章中讨论这个问题。但是替换目标更简单:这意味着当你已经命名了捕获组时，你可以在目标字符串的`replace`函数中使用它们。语法是在目标字符串中使用`$<name>`。

例如，这里我们将在替换的字符串
中使用捕获的`subdomain`

```
const regex = /(?<host>(?:(?:https?:)?\/\/)?(?<subdomain>.*)\.google\.com)(?<path>.*)/i;

'https://www.google.com/search?q=devto'.replace(regex, 'subdomain: $<subdomain>');

// Result
'subdomain: www' 
```

Enter fullscreen mode Exit fullscreen mode

就这样，伙计们！我希望你今天过得非常愉快！