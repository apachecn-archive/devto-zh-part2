# 紧凑代码的详细解释

> 原文：<https://dev.to/jdsteinhauser/a-verbose-explanation-of-compact-code-546g>

*封面图片发现于[imgur.com](http://i.imgur.com/pKpb3wz.jpg)T3】*

我妻子是个天才。她非常聪明。她是一名天体物理学家，热衷于过程和产品改进。然而，对于我来说，我还没有找到一种有效的方式来和她谈论我写的代码的细节。当然，我们讨论工作中发生的事情的大部分时间是在晚餐时，两三个孩子(取决于课外活动)很吵，所以我必须简洁。我可以很容易地回答“有多少个 SLOC”、“你怎么能在其他地方重用它”、“它有什么用”和“你为什么选择那种语言”。但是当她想要更多的技术细节时...我还没有找到一种既简洁又能表达理解的方法。所以这篇文章是献给几周前我和我妻子的一次谈话，关于我那天在工作中完成的一项任务。

# 工作范围

我有一个客户，他非常喜欢尽可能地自动化，这样他的人(比如我和实验室里的其他人)就可以最大限度地利用我们花在代码上的时间。到目前为止，他是一个非常好的客户，我主动写了一些东西:一个自动化的任务，将我们本周发现和/或处理的所有发现的状态汇总在一起，生成一个 PDF，并将其发送给他的老板。我是说，这能有多难？

# 技术选择

我的客户喜欢让事情尽可能简单，并希望在 Markdown 中构建我们的报告，将我们在 JIRA 每周报告的发现放在一个表格中。这将需要展平来自 [JIRA REST API](https://developer.atlassian.com/server/jira/platform/rest-apis/) 的响应，并打印出适用的数据，在有空值的地方用“TBD”表示。哦，他希望能够在多个报表中重用它，因为不同的表列可能会发生变化。

在这一点上，我已经为自己选择了一些东西:

*   JIRA 输入
*   降价输出

现在，我所要做的就是选择一种语言。终于看到了在制作中使用我的宠物项目语言(Clojure)的地方！这不仅仅是一个异想天开的选择；在最终选择 Clojure 之前，我评估了几种不同的语言。

# 四分五裂的 JIRA 回应

这个项目并没有花很长时间；我的大部分时间都花在 Chrome 开发工具上，计算定制字段的 id，这样我就可以将数据结构简化成一个简单的 HashMap。因为我必须处理的只是 JIRA 回应的`issues`部分，所以我选择把每个问题都当作它自己的“对象”。这提供了从响应到表格的良好映射，因为每个 JIRA 问题都将成为表格中自己的一行。我创建了一个类似于下面的函数:

```
(defn  issues->useable  [issue]  {  :finding-id  (str  "["  (:key  issue)  "]("  (:self  issue)  ")")  :title  (get-in  issue  [:fields  :summary])  :status  (get-in  issue  [:fields  :status  :name])  :priority  (get-in  issue  [:fields  :priority  :name])  :date-created  (get-in  issue  [:fields  :created])  :components  (->>  issue  :fields  :components  (map  :name)  (str/join  ","))  :due-date  (get-in  issue  [:fields  :duedate])  :labels  (->>  issue  :fields  :labels  (str/join  ","))  :risk-consequence  (get-in  issue  [:fields  :customfield_22007  :value])  :risk-probability  (get-in  issue  [:fields  :customfield_22006  :value])  }) 
```

Enter fullscreen mode Exit fullscreen mode

是的，我在函数命名上有点厚脸皮。是的，我知道这里有很多代码需要消化。我会一步步来。

## 输出

对于以前没有看过 Clojure 的人来说，这个函数的输出不是很明显。Clojure 通常将数据结构表示为 HashMaps。以“:”开头的记号表明它们是一个`keyword`，很像仙丹中的印记或者红宝石中的符号。稍后我会详细解释为什么我说“大部分”，因为这对于选择 Clojure 至关重要。在 Clojure 中，map 用花括号括起来，键和值组合在一起。例如，

```
{:a  1,  "foo"  "bar"} 
```

Enter fullscreen mode Exit fullscreen mode

是一个带有键/值对`:a => 1`和`"foo" => "bar"`的映射。是的，Clojure 可以使用非关键字作为键，但这在实践中并不常见。两对之间的逗号实际上是可选的；Clojure 将所有逗号视为空白。所以我的`issues->useable`函数的输出是一个带有关键字/字符串对的地图。

`get-in`功能也相当不错。给定一个嵌套 hashmap 和一系列键，它将向下解析嵌套 hashmap 并返回存储在那里的值。如果没有与结束键相关联的值，则返回`nil`。

其他一些 Clojure 概念也在这段代码中得到了体现。如果我们看看`components`字段是如何在我的输出图中生成的，您会看到一个`->>`宏。`->>`是一个线程结束宏，它将前一个函数求值的输出作为下一个函数求值的最后一个参数。所以对于

```
 :components  (->>  issue  :fields  :components  (map  :name)  (str/join  ",")) 
```

Enter fullscreen mode Exit fullscreen mode

首先，将`issue`传递给*函数* `:fields`。

### 我以为:fields 是一个关键字！

这是我选择 Clojure 的主要原因。Clojure 中的关键字也实现了`IFn`接口，意味着它们被认为是 Clojure 中的函数。因此，表达式`(:a {:a 5 :b 6})`将计算为`5`。它返回与关键字`:a`相关的值。

当然，我可以把函数的这一部分写成`(:fields issue)`，但是当我想进一步深入时，我必须把整个函数写成

```
(str/join  ","  (map  :name  (:components  (:fields  issue)))) 
```

Enter fullscreen mode Exit fullscreen mode

这远不如使用`->>`宏干净。通过在 Clojure 中使用前线程和后线程宏，您能够更清晰地看到数据管道，类似于您在 OCaml、F#或 Elixir 中使用`|>`操作符(及其变体)的方式。

### 创建表格行

所以现在我可以将一个问题转换成一个扁平化的地图，但是我需要将它作为一个减价行。Markdown 用竖线(`|`)分隔其表格列，竖线位于整行框的外侧。为此，我编写了以下函数:

```
(defn  create-row  [values]  (->>  values  (map  (fnil  name  "TBD"))  (into  '(nil))  (cons  nil)  reverse  (interpose  "|")  (apply  str))) 
```

Enter fullscreen mode Exit fullscreen mode

所以，我再次从后线程宏和*开始，只从我的键/值对中选择值*。然后我将`(fnil name "TBD")`映射到每个值上。`fnil`返回一个高阶函数，该函数接受传递给它的`nil`参数，用一个不同的指定值(在我们的例子中是“TBD”)替换它们，并调用作为第一个参数列出的函数。我使用了`name`来返回 map 中值的字符串化版本(或者在 nil 的情况下返回“TBD”)。当然，我可以用`identity`函数代替`name`，但是那要多 4 个完整的字符；-)我喜欢`fnil`仅仅是因为它减少了 NULL/nil 检查逻辑的占用空间。
基于我之前的代码，下一行可能看起来有点奇怪:
`(into '(nil))`将刚刚被字符串化/“TBD”的值放到一个只包含值`nil`的列表中。我将在一秒钟内解释这一点，以及`(cons nil)`行。对`reverse`的调用应该相当清楚:颠倒序列中项目的顺序。

使用上述`nil` s 的原因是为了在`interpose`功能中使用。`interpose`接受一系列项目，并在它们之间插入指定的值。例如，`(interpose 1 [3 4 5])`将产生序列`(3 1 4 1 5)`。然而，我在序列的开始和结束处也需要管道，作为表的“外壁”。所以我使用`(into '(nil))`将序列中的值传递到一个只有`nil`的列表中。由于`into`对每个元素执行一个`conj`操作，我们刚刚创建的序列中的元素将以相反的顺序添加。例如`(into '(nil) [1 5])`将产生`(5 1 nil)`。如果一个`cons`一个`nil`放到那个集合的前面，那么我就有一个`(nil 5 1 nil)`的序列。插入管道，然后给我`(nil "|" 5 "|" 1 "|" nil)`。

然后，我将`str`函数`apply`应用到我们刚刚创建的集合中。`str`函数通过连接传递给它的参数来创建一个字符串，而`nil`产生一个空字符串。`apply`对我们现在反转的集合`(nil "|" 1 "|" 5 "|" nil)`进行操作将得到字符串“|1|5|”。完美！这正是我们在 Markdown 中创建表格行所需要的。

### 创建整个表格

我现在有一个函数来创建我的行，但是行在没有标签的表中不是很有用。因此，我们需要一个函数来创建整个表，完成数据的“列”是哪个。我可以用 6 行非常密集的 Clojure 代码生成这些代码:

```
(defn  create-table  [ordered-keys  issues]  (let  [header  (create-row  (map  ->PascalCase  ordered-keys))  separator-row  (create-row  (repeat  (count  ordered-keys)  "---"))  rows  (map  (comp  create-row  (apply  juxt  ordered-keys))  issues)]  (apply  vector  header  separator-row  rows))) 
```

Enter fullscreen mode Exit fullscreen mode

让我们走一遍。函数`create-table`接受一个有序键的列表，以及扁平化的问题(或者，基本上，任何平面 hashmap ),并生成一个 Markdown 表。我还为三个局部变量赋值:`header`、`separator-row`和`rows`。让我们看看使用这些变量的表达式:`(apply vector header separator-row rows)`。`vector`函数获取一个参数列表，并从这些参数中创建一个向量(即`[1 2 3]`而不是类似于`(1 2 3)`的列表)。在本例中，我创建了一个表格行的向量，从标题开始，在标题和值之间有一个需要标记的分隔符行。`header`仅仅是使用必不可少的 [camel-snake-kebab](https://github.com/qerub/camel-snake-kebab) 库将有序键从 keywords 转换为 PascalCase 而创建的一行。这样，`:finding-id`将被打印成更易于管理的`FindingId`。其次，分隔符行只是三个破折号，重复的次数与列数一样多。现在，在这个项目中，我最喜欢 Clojure 的一行，仅仅是因为它强大的功能:

```
rows  (map  (comp  create-row  (apply  juxt  ordered-keys))  issues) 
```

Enter fullscreen mode Exit fullscreen mode

显然，我正在对传入的所有行执行函数 ping 操作。这个功能相当复杂。`juxt`是 Clojure 中我最喜欢的函数之一。它接受一个函数序列，并返回一个函数，该函数将相同的参数传递给该序列中的所有函数，并返回一个结果向量。例如，`((juxt + *) 3 4)`产生`[7 12]`。当我主要做。NET 开发中，我移植了这个函数和它的多个算法，这样我就可以在一次操作中计算多个独立的 TB 级数据指标。使用`apply juxt`到我们的有序键列表(都是关键字)，我刚刚创建了一个函数，当评估我们的数据时，它将按照指定的顺序生成与我的数据中的关键字相关联的值的向量。例如，`((juxt :a :id) {:a 5 :b 60 :id "blue"})`将产生`[5 "blue"]`。然后我用 create-row 函数组合这个函数(函数`comp`),因为

```
(map  create-row  (map  (apply  juxt  ordered-keys))  issues) 
```

Enter fullscreen mode Exit fullscreen mode

只是看起来不干净。

# 结论

所以基本就是这样！这是一大堆密集的代码，很难一下子理解，但是一旦你理解了它的用法，就会变得非常灵活。我现在可以从任意一组数据中创建多个表，而不局限于任何特定类型的数据。我可以将我的 JIRA 问题(在本例中)传递到我的`create-table`函数中，用不同的有序键集合来创建不同的报告——例如，一个表格对中层管理人员向上层管理人员报告有用，另一个表格供开发人员了解他们正在处理的问题的可见性。我也不依赖于 JSON 或任何其他格式的数据。只要我能把数据放入地图，我就能重复使用它来生成减价表。

我知道这是一个相当长的阅读，所以祝贺你，如果你通过了！如果你有一些特别复杂但有用的代码，我也很乐意看到你写出来。如果您还没有探索 Clojure，我欢迎您尝试一下，看看这些和其他一些令人兴奋的语言特性:-)