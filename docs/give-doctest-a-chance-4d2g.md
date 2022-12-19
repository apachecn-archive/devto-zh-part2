# 给 Doctest 一个机会

> 原文：<https://dev.to/adambrandizzi/give-doctest-a-chance-4d2g>

*(原载[此处](http://suspensao.blog.br/disbelief/doctest/)。)*

Doctest 是我最喜欢的 Python 模块之一。使用 doctest，可以执行文档中的代码片段。例如，你可以在你的`turorial.md`中写下这样的内容...

```
>>> f()
1 
```

Enter fullscreen mode Exit fullscreen mode

...然后执行命令`python -mdoctest tutorial.md`。如果`f()`返回 1，什么都不会发生。但是，如果它返回其他内容，就会出现一条错误消息，类似于下面这条:

```
**********************************************************************
File "f.txt", line 2, in f.txt
Failed example:
    f()
Expected:
    1
Got:
    2
**********************************************************************
1 items had failures:
   1 of   2 in f.txt
***Test Failed*** 1 failures. 
```

Enter fullscreen mode Exit fullscreen mode

这是一个令人印象深刻的工具，但也是一个不受欢迎的工具。问题是，Doctest 经常被不恰当地使用。例如，尝试用 doctests 编写单元测试是很常见的。大错特错。

尽管如此，我认为由于这些误解而忽视这个模块是不公平的。Doctest 可以而且应该用于它最擅长的领域:让您的文档保持活力，甚至指导您的开发！

让我举个例子。

### 不知如何是好的时候

几天前，我正在编写一个使用`xml.dom.minidom`修改 HTML 文档的类。有一次，我需要一个函数将 CSS 类映射到文档中的节点。仅此一项就将是一个复杂的函数！我不知道从哪里开始。

理论上，单元测试在这里可能是有用的。它们不太实用:这是一个内部的私有函数，是一个实现细节。为了测试它，我必须暴露它。我们还需要一个新文件，用于测试。而且测试用例也不是那么清晰可辨。

### 阅读来自未来的文档

相反，我首先记录了这个函数。我写了一小段描述它会做什么。仅此一点就足以澄清我的想法:

> 给定一个 xml.dom.minidom.Node，返回从每个“class”属性到具有该类的节点列表的映射。

然后，我考虑如何写同样的东西，但是用一个代码例子。在我的脑海中，这个函数(我称之为`get_css_class_dict()`)将接收`xml.dom.minidom`文档。所以，我写了一个例子:

```
 >>> doc = xml.dom.minidom.parseString(
    ...     '''
    ...     <html>
    ...         <body>
    ...             <div class="a b"><span class="a"></span></div>
    ...         </body>
    ...     </html>
    ...     ''') 
```

Enter fullscreen mode Exit fullscreen mode

给定这个片段，我希望函数返回一个字典。我的文档有两个 CSS 类，“a”和“b”，然后我的字典有两个键。每个键都有一个 CSS 类的节点列表。大概是这样:

```
 >>> d = get_css_class_dict(doc)
    >>> d['a']  # doctest: +ELLIPSIS
    [<DOM Element: div at ...>, <DOM Element: span at ...>]
    >>> d['b']  # doctest: +ELLIPSIS
    [<DOM Element: span at ...>] 
```

Enter fullscreen mode Exit fullscreen mode

我把这些草图放在`get_css_class_dict()`的[文档串](https://pythonhelp.wordpress.com/2011/02/14/docstrings/)中。到目前为止，我们有这个功能:

```
def get_css_class_dict(node):
    """
    Given an xml.dom.minidom.Node, returns a map from every "class" attribute
    from it to a list of nodes with this class.

    For example, for the document below:

    >>> doc = xml.dom.minidom.parseString(
    ...     '''
    ...     <html>
    ...         <body>
    ...             <div class="a b"><span class="a"></span></div>
    ...         </body>
    ...     </html>
    ...     ''')

    ...we will get this:

    >>> d = get_css_class_dict(doc)
    >>> d['a']  # doctest: +ELLIPSIS
    [<DOM Element: div at ...>, <DOM Element: span at ...>]
    >>> d['b']  # doctest: +ELLIPSIS
    [<DOM Element: span at ...>]
    """
    pass 
```

Enter fullscreen mode Exit fullscreen mode

我可以用单元测试做类似的事情，但是会有更多的代码，污染文档。此外，散文优雅地补充了代码，给阅读带来了节奏。

我执行了文档测试，结果如下:

```
**********************************************************************
File "vtodo/listing/filler.py", line 75, in filler.get_css_class_dict
Failed example:
    d['a']
Exception raised:
    Traceback (most recent call last):
      File "/usr/lib/python3.6/doctest.py", line 1330, in __run
        compileflags, 1), test.globs)
      File "<doctest filler.get_css_class_dict[2]>", line 1, in <module>
        d['a']
    TypeError: 'NoneType' object is not subscriptable
**********************************************************************
File "vtodo/listing/filler.py", line 77, in filler.get_css_class_dict
Failed example:
    d['b']
Exception raised:
    Traceback (most recent call last):
      File "/usr/lib/python3.6/doctest.py", line 1330, in __run
        compileflags, 1), test.globs)
      File "File "<doctest filler.get_css_class_dict[3]>", line 1, in <module>", line 1, in <module>
        d['b']
    TypeError: 'NoneType' object is not subscriptable
**********************************************************************
1 items had failures:
   2 of   4 in filler.get_css_class_dict
***Test Failed*** 2 failures. 
```

Enter fullscreen mode Exit fullscreen mode

我基本上遵循测试驱动开发，但是使用可执行文档。我立刻得到了一个可读的例子和一个基本的测试。

现在，我们只需要实现功能！我使用了一些递归，如果代码一开始不是最简洁的...

```
def get_css_class_dict(node):
    """
    Given an xml.dom.minidom.Node, returns a map from every "class" attribute
    from it to a list of nodes with this class.

    For example, for the document below:

    >>> doc = xml.dom.minidom.parseString(
    ...     '''
    ...     <html>
    ...         <body>
    ...             <div class="a b"><span class="a"></span></div>
    ...         </body>
    ...     </html>
    ...     ''')

    ...we will get this:

    >>> d = get_css_class_dict(doc)
    >>> d['a']  # doctest: +ELLIPSIS
    [<DOM Element: div at ...>, <DOM Element: span at ...>]
    >>> d['b']  # doctest: +ELLIPSIS
    [<DOM Element: span at ...>]
    """
    css_class_dict = {}

    if node.attributes is not None and 'class' in node.attributes:
        css_classes = node.attributes['class'].value

        for css_class in css_classes.split():
            css_class_list = css_class_dict.get(css_class, [])
            css_class_list.append(node)
            css_class_dict[css_class] = css_class_list

    childNodes = getattr(node, 'childNodes', [])

    for cn in childNodes:
        ccd = get_css_class_dict(cn)
        for css_class, nodes_list in ccd.items():
            css_class_list = css_class_dict.get(css_class, [])
            css_class_list.extend(nodes_list)
            css_class_dict[css_class] = css_class_list  

    return css_class_dict 
```

Enter fullscreen mode Exit fullscreen mode

...至少像预期的那样工作:

```
$ python -mdoctest vtodo/listing/filler.py 
**********************************************************************
File "vtodo/listing/filler.py", line 77, in filler.get_css_class_dict
Failed example:
    d['b']  # doctest: +ELLIPSIS Expected:
    [<DOM Element: span at ...>]
Got:
    [<DOM Element: div at 0x7f3e972e3508>]
**********************************************************************
1 items had failures:
   1 of   4 in filler.get_css_class_dict
***Test Failed*** 1 failures. 
```

Enter fullscreen mode Exit fullscreen mode

等一下。那是什么？！

### 单据有误时

嗯，我的文件测试有一个错误！span 元素没有“b”类 div 元素有。所以，我只需要改变路线

`[<DOM Element: span at ...>]`

到

`[<DOM Element: div at ...>]`

Doctest 就会通过。

是不是很奇妙？我几乎立刻就在我的文档中发现了一个错误。不仅如此:如果某一天我的函数的行为改变了，我的 docstring 中的例子将会失败。我会确切地知道哪里的文档需要更新。

### 让 doctests 物有所值

这就是 Doctest 背后的基本原理。我们的文档有一个微妙的错误，我们通过执行它发现了它。文档测试不保证代码的正确性；它们加强了文档的正确性。这是一揽子计划中众所周知的一个方面，但似乎很少有人相信这是值得的。

我觉得是！文档通常被认为是令人不愉快的工作，但事实并非如此。正如 TDD 使测试令人兴奋一样，使用 doctests 也可以使文档变得有趣。

除此之外，同样地，TDD 可以指出设计的局限性，写文档测试的困难可以指出 API 的问题。如果很难为您的 API 编写一个清晰简洁的使用示例，并附上解释文本，那么它可能太复杂了，对吗？

### 给 Doctest 一个机会

最后，我看到了 doctests 的局限性。例如，它们肯定不足以进行单元测试。然而，doctest 使记录变得如此简单和有趣！我不明白为什么它如此不受欢迎。

尽管如此，它最大的优势是 doctest 如何使*开发过程*更容易。前段时间我开玩笑说我们需要创建 DocDD:

> ![Adam Brandizzi profile image](img/56cae89bdb610015ba1ef32f54c7fa58.png)亚当布兰迪齐[@亚当布兰迪齐](https://dev.to/adambrandizzi)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我需要发明文档驱动开发。我正在这里写一些文档字符串。很多事情都错了！2015 年 6 月 10 日上午 00:27[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=608430368233017344)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=608430368233017344)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=608430368233017344)

有了 Doctest，它不再只是一个笑话。

<small>*本帖是 [Dê uma chance a Doctest](http://suspensao.blog.br/descrenca/doctest/) 的翻译。*</small>

<small>*封面图片来自[维基共享资源](https://commons.wikimedia.org/wiki/File:Carstian_Luyckx_-_Vanitas_Still_Life_with_Celestial_Globe,_a_Book,_Shells,_a_Snake_and_Butterflies_.jpg)。*</small>