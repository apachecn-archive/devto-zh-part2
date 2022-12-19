# 使用 picolisp 探索原始 XML

> 原文：<https://dev.to/cess11/raw-xml-exploration-with-picolisp-7hn>

下面是一个关于使用 pil VM、DB 和 OOP 在 XML 结构化数据中进行基本数据挖掘的简短教程。本文假设对 POSIX 或至少是 unixlike 的编程和环境有所了解。关于安装 picolisp 的信息，请访问 picolisp.com。

# 如何 lisp 一些 XML

今年我们将在瑞典举行选举。为了做好准备，不妨回顾一下 2014 年的上一轮选举。

幸运的是，瑞典政府为我们提供了全套的投票结果。

```
wget http://www.val.se/val/val2014/slutresultat/slutresultat.zip

unzip slutresultat.zip 
```

这将在一个新目录中解包大量 ISO-8859-1 编码的 XML 文件，以及关于其余部分的一些元数据文件。很容易把它们转换成 UTF-8，[就像这个](https://dev.to/cess11/first-post-picolisp-script-mok)。

其中大部分包含了国家议会、地区议会或地方议会的投票结果。文件名由一个数字郡代码和一个代表每次选举的字符构成，R 代表议会(“riksdag”)，L 代表地区(“landsting”)，K 代表地方议会(“kommun”)。

在有数据的目录中，我们然后开始 pil。

```
$ pil +
: 
```

不是很刺激。让我们通过将其中一个文件附加到一个符号上并做一些简单的处理来看看它。

```
:(length 
    (setq A 
        (make 
            (in "slutresultat_1407L.xml" 
                (until (eof)(link (line T]
-> 1832
:(head 10 A) 
```

正如 head 所示，这实际上是 XML 数据。既然是这样我们也知道在每一个'

为了提取这个，我们可以使用这样的表达式。

```
:(length 
    (setq *Subjects 
        (uniq 
            (make 
                (for X A 
                    (and 
                        (or 
                            (match '("<" @Subject " " @B ">") (chop X))
                            (match '(@C "<" @Subject " " @B ">") (chop X)))
                        (link (pack @Subject]
-> 17
:*Subjects 
```

在实际的挖掘会话中，人们最有可能使用“de”并将其转换为一个函数，该函数将一个列表(用于“匹配使用”)作为参数，并具有符号和全局变量*Subjects(可能有另一个名称)用“匹配”的结果更新的副作用。当进行交互式工作时，全局变量可以像任意大小的便利贴一样，保存从数百万个文本片段到对象引用或空空如也的任何东西，而这些会话的临时性质可以抵抗生产代码中的全局变量的恐怖。

这里的“length”用于确保表达式返回既简短又有用的内容，即我们搜索中唯一命中的数量。setq 返回符号的内容，这意味着它将用垃圾填满屏幕，除非我们的表达式是合理的或者数据集非常小(谁会希望这样呢？).

“Picolisp”匹配通常在使用“grep”或 regexp 的情况下很有用。它的第一个参数是一个与第二个参数匹配的列表，当匹配成功时,@Variables 会获取数据源中该结构位置的内容。这意味着它非常适合复杂的渗出技术以及这里显示的基本技术。

如您所见，我们现在有一组 17 个唯一的 XML 标记名。这些可以提供给我们前面的表达式，以提取更多的信息。我们来看看“PARTI”包含了哪些内容。

```
:(length 
    (setq *PARTI
        (uniq 
            (make 
                (for X A 
                    (and 
                        (or 
                            (match '("<" ~(chop "PARTI") " " @B ">") (chop X))
                            (match '(@C "<" "P" "A" "R" "T" "I" " " @B ">") (chop X)))
                        (link (pack @B]
-> 21
:*PARTI 
```

波浪号(~)使它所附加的表达式被计算为常规 lisp，在将结果与下一个参数中的列表进行匹配之前，这里的' chop '将像" PARTI "这样的瞬态符号转换为序列" P" "A" "R" "T" "I "。如果很容易省略“chop one should ”,或者至少用它来为“match”准备一个可以重用的列表，因为它会带来一些开销，对于较大的数据集来说非常明显。

显然有 21 个政党(“PARTI”的意思是“党”)参加了该县的地区选举(其名称在“KOMMUN”标签的“NAMN”属性中)。

让我们写出他们的名字。

```
:(mapcar 
    '((X)
        (and 
            (match '(@A " " @B "\"" @Party "\"" @C) (chop X))
            (printsp (pack @Party)))) 
    *PARTI) 
```

这种表达式特别有用，因为‘mapcar’的第一个参数可能会产生各种副作用，而‘mapcar’本身将返回所有成功匹配的列表，而不考虑这些副作用。

其中一个潜在的用途是将渗出的数据或者整行或整个文档保存在数据库对象中，只需简单地将“new！子句，并设置了一些类来保存和组织感兴趣的数据。

它可能看起来像这样:

```
:(class +GeneralContainment +Entity)
-> +GeneralContainment
:(rel tag (+Ref +String))
-> +GeneralContainment
:(rel dta (+List +String))
-> +GeneralContainment
:(mapcar 
    '((X)
        (and 
            (match '(@A "=" "\"" @Short "\"" @B "=" "\"" @Full "\"" @C) (chop X))
            (new! '(+GeneralContainment) 
                'nm "Party names, (abbreviation . name)" 
                'dta (cons 
                    (pack @Short) 
                    (pack @Full))))) 
    *PARTI) 
```

这样的类名并不特别习惯，这里的要点是这些类应该非常通用，并保存在一个小的实用程序库中。通常，上面的结构或具有两个类的结构(除了名称/类别字段和一个用于数据的字段之外)足以构成大多数交互式挖掘，其中一个类可以包含与另一个类的对象的许多'+联合关系。

一旦掌握了基本的列表操作、文本解析技术和 pilog 表达式，就可以更容易地直接渗透到通用对象中，而不是涉猎全局对象，特别是对于较大的数据集，但是也应该使用一些助手方法，在不阻塞 REPL 的情况下进行渗透，因为数据库操作可能需要一些时间。

感谢阅读，欢迎提问和评论。

Stagling@Patreon