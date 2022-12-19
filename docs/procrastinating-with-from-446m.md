# 用“从”来拖延

> 原文：<https://dev.to/cess11/procrastinating-with-from-446m>

## 简介

由于我很难找到时间来完成那篇留言簿文章，我将用一些更具交互性的解析来让您感到厌烦，这一次我们将挤出并保存一个远程数据库子集的副本，我们可以通过浏览器或类似的 web 客户端访问该数据库。

之前我写过一点关于如何使用 picolisp 函数的 match 来探索 XML 或类似结构的数据。这是一个非常强大的模式匹配工具，可以用来从任何数据集中提取任何数据，只要你知道它的环境是什么样的。

不过，有时你想要别的东西，但又不知道如何找到一个有效的匹配表达式。也许您想在重复的数据集中提取一个模式的每一次出现，但是还不确定这个模式应该是什么样子。如果是这种情况，人们通常使用成对的函数“from”和“till ”,而不是以稍微不同的方式。

我已经将这种技术用于各种交互式搜索和模式匹配，包括真正的工作应用程序，如在数十亿字节的网站中搜寻联系信息。

## 模型

前几天我陷入了困境，我有一些文件包含来自 metal-archives.com 的瑞典金属乐队的标记和信息，我想提取这些信息并使其可搜索。基本上是在本地创建他们数据库的一个子集，并查询我感兴趣的细节。

正如您在链接中看到的，它大约有 9 页，如果您在 JavaScript 从 Ajax 请求和一些 HTML 组装 DOM 之后查看它，您会看到搜索结果是一行重复的 tbody-tr-TD-构造。这使得很难一行一行地解析出一个模式，对于这个模式来说,' match 应该是一个完美的匹配。

取而代之的是，我们要弄清楚我们在序列模式片段中想要什么。

如何保存 JS 生成的动态 DOM 的副本，这本身是一门小科学，我可以很方便地假设您已经在这方面做过手脚。至于我的设置，我用一个 Chromium 实例完成了，并很快获得了 9 个 HTML 文件来检查和编写一个小的解析器。

我们想要做的是让 picolisp 阅读器查看 HTML 文件中的搜索结果行，并返回一些数据结构，其中包含乐队名称、位置、状态、流派和 URL。因此，我们启动它，并告诉它我们的数据模型应该是什么样子，以及在什么文件中保持它的持久性。

```
$ pil +
: (pool "mase.db") 
-> T
: (class +Band +Entity)
-> +Band
: (rel nm (+Ref +String))
-> +Band
: (rel loc (+Ref +String))
-> +Band
: (rel status (+Ref +String))
-> +Band
: (rel genre (+Ref +String))
-> +Band
: (rel url (+Ref +String))
-> +Band
: 
```

或者作为 er.l 中的片段:

```
(pool "mase.db") 

(class +Band +Entity)
(rel nm (+Ref +String))
(rel loc (+Ref +String))
(rel status (+Ref +String))
(rel genre (+Ref +String))
(rel url (+Ref +String)) 
```

然后可以用'(load "er.l ")加载。

## 从

现在您已经有了模型、HTML 文件和池化数据库，是时候开始研究我们的解析器了。当对一组新数据执行此操作时，很可能通过将一个文件读入一个变量，作为一个瞬态符号列表、一个字符串列表或一个行列表，从 REPL 中进行一些检查。你也可以使用文本编辑器或浏览器中的“查看源代码”,当你想浏览并快速了解有价值的信息在哪里以及数据在那里是如何组织的时候，无论什么都可以。

看了看 1.html，发现这条线有大约 500 个波段和一些数据，我们可以开始想办法把它弄出来。from 就像一个钩子，而‘till’是一个释放，所以你可以从它看起来应该从哪里开始提取，以及‘到什么时候停止’。

```
: (make # we want this to return a list
    (in "1.html" # from this file
        (until (eof) # and keep going until the end
            (link # linking lists into a list of lists
                (make 
                    (from "tr class") # from this pattern, look for
                        (from "href=\"") # this pattern, and then 
                            (link (pack (till "\""))) ] # grab it. 
```

如果你在你的 REPL 上运行这个，并且把 HTML 文件按顺序排列好，你会得到一个 URL 列表:s，类似这样的东西，但是大约有 500 个元素长'(("[https://www . metal-archives . com/bands/Name/ID ")(" https://www . metal-archives . com/bands/Another _ Name/Another ID ")](https://www.metal-archives.com/bands/Name/ID%22)(%22https://www.metal-archives.com/bands/Another_Name/AnotherID%22)))。

这是由“make-”链接结构构建的，其中最里面的一个链接结构从“pack”中获取 URL 作为一个临时符号。如果我们不使用' pack ',我们将得到 URL:s 作为字符列表。

然而，仅仅获得这些链接是不令人满意的。我们还需要其余的信息，所以我们将像这样扩展上面的内容。

```
: (make 
    (in "1.html" 
        (until (eof) 
            (link 
                (make 
                    (from "tr class")
                        (from "href=\"")
                            (link (pack (till "\""))) 
                                (from ">") # name is between >
                                    (link (pack (till "<"))) # and <
                                        (from "><")(from "><") # after two >< 
                                            (from ">") # then a >
                                                (link (pack (till "<"))) ] 
```

正如你所看到的，我们可以对数据中的细节一无所知，并提取我们想要的东西，只要知道会有两个' >数据

为了从这些数据中获取所有有趣的信息，我们将继续添加“from expressions”，下一个将查找单个“>”、“till”、“till”

## 把它放在物体上

一旦所有这些都准备就绪，我们就得到一个列表，其中每个元素都是一个列表(Url 名称位置类型状态)，这使得将它保存为数据库对象变得非常简单。假设您在 REPL 中运行它并得到结果，您可能会这样做，其中@在常规的 REPL 上下文中是“结果堆栈中的最后一个”的缩写，而@@和@@@则需要深入研究。

```
: (for X @ 
    (new! '(+Band)
        'nm (cadr X)
        'loc (caddr X)
        'status (last X)
        'genre (cadddr X)
        'url (car X) ] 
```

一次新的！已经成功运行，您的数据是持久的，可浏览和可搜索的。要非常非常确定它在您的数据库中，并且在您下次打开它时可用，请额外运行一次'(commit 'upd '。

从这里很容易把它们放在一起，用' dir 为结果函数提供一个参数列表。最简单但有点难看的方法是用 nest 中的完整“make-”替换@ above，并从“dir”中获取文件名，这样当它遍历完所有文件后，最终会得到许多数据库对象，其中包含 Metal-Archives 已知的全套瑞典金属乐队。

当然，更好的方法是将它分解到文件中的几个函数中，至少如果你想在“from”till 结构中重用基本的“make ”,然而，它作为一个交互工具确实很出色，有时这意味着牺牲一点优雅，而采用更野蛮的代码风格。

## 原语即席查询

可以通过几种方式查询数据库。我们使用了一个简单的'+Ref 索引，这样可以容忍子串搜索，但我们可以用我们的老朋友' match '和' printsp '函数来模拟这种情况，它打印带有空格的内容。

```
: (for X (collect 'nm '+Band) 
    (with X
        (and 
            (match '("S" "t" "o" "c" "k" "h" "o" "l" @B) (chop (: loc)))
            (printsp (pack (: nm) " - " (: loc))) ] 
```

使用像“@B 等于通配符”这样的变量，这种模式将适用于所有以“Stockhol”开头的位置字段(即下一行将触发“printsp name and location”)，以及那些看起来像“Stockholm/SomeOtherCity”的字段。在实践中这样做时，很可能在开头放一个“@A ”,并且在文件中使用“use”来声明这些符号并防止它们从程序的其他部分泄漏工件。

## 结束

如果你想让 REPL 尽快完成，我在这里保留了 10 分钟的时间这基本上是上述内容的重复。

如果你认为这值得一读，可以考虑捐赠一两杯咖啡。

上面使用的数据可能是受版权保护的，如果是这样的话，我认为这是一个优秀的网络服务的合理使用和体面的广告，但如果合适的人告诉我，我当然会删除帖子。在做这件事的时候，我听了[巴力庙，火的诗句](https://www.youtube.com/watch?v=Lcj3biWucpU)。

附录:很抱歉之前断开的链接，现在已经修复了。谢谢 [aw](https://picolisp.a1w.ca/) 指出来。