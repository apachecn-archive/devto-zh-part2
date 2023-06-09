# 使用 Logoot 进行协作文本编辑

> 原文：<https://dev.to/ravernkoh/collaborative-text-editing-with-logoot-2gh0>

最近，我对协作文本编辑的工作方式产生了兴趣。我在一个小组里做学校的作业，我们用谷歌文档来工作。一个人可以输入一些东西，几秒钟后它就出现在其他人的屏幕上，而没有任何冲突，这看起来就像是魔术。我最近还发现 Keynote 和 Notes 应用程序内置了协作功能。所以我决定深入研究协作文本编辑。

## 协作的问题

要编辑一个纯粹基于文本的文档，实际上只需要做两个基本操作:插入和删除。任何其他操作都建立在这两个操作之上。单个用户编辑一个文档可能看起来像这样。

[![Single user editing a document](img/878ea4fd86d4f22639dd92a90e44c69d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xl1acW9b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9kcjnw3sq96tv06790n.png)

要在文档中进行协作，最简单的方法是将用户执行的每个操作发送给编辑文档的所有其他用户。当连接速度很快时，可能是当用户连接到局域网时，这种方法是有效的。

[![2 users editing a single document over a LAN/very fast Internet connection](img/7abd226497f499382f8183adb570adad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fL_wujVN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n6exl8cigf8tgroe66ji.png)

然而，当网络延迟变得很大时，就很容易达到这样的竞争条件。

[![2 users editing a single document with network latency](img/e8f5a86102d99b7cc5265ae9ea9780ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hKOMoN6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yuf29wg728hoy9ei0bbn.png)

当这些争用情况发生时，两个客户端上的文档变得不同步，并且由于这些操作的性质，它们很少会再次同步。因此，我们需要一种更好的方法来组织这些操作，甚至是组织文档本身，这样可靠和一致的协作编辑才能成为现实。

## 运算转换

进入运营转型，简称 OT。OT 是一种算法，无论从其他客户端发送的操作顺序如何，它都将保持所有客户端之间文档的一致性。wave(Google Docs 背后的协作引擎)使用 OT。我没有深入研究 OT，因为我发现了其他更简单、更直观的解决方案，我将在后面解释。但是现在，这里有一个关于基本 OT 如何工作的快速纲要。

### 快速纲要

让我们从了解加班的基本知识开始。如前所述，文档只是一些内容，可以通过插入或删除来操作。OT 解决并发编辑问题的方法是在将操作应用于文档之前，使用前一个操作通过*转换*每个操作。使用前面的例子，在客户机 A 对他们的文档执行插入操作之后，客户机 B 的删除操作，也就是下一个要执行的操作，被转换。

转换后，我们可以看到操作现在从文档中删除了正确的字符，而不是简单示例中的错误字符。同样，在客户端 B 的文档上，客户端 A 的插入操作在客户端 B 的删除操作执行之后被转换，并且结果文档在两端是相同的。

### 降级为 OT

不幸的是，大多数流行的 OT 算法并不总是可交换的。这意味着当将 OT 应用于两个独立的文档时，并不总是能保证它们最终是相同的。虽然这种情况很少发生，但也有可能发生，这将导致每隔一段时间就采取一些措施来确保一致性。因此，近年来出现了一种不同的协作编辑方式，称为 CRDTs。

## crdt

实际上有两种口味的 CRDTs。它们是(请原谅)交换复制数据类型和收敛复制数据类型。下面就讲前者。CRDTs 采用了一种不同于 OT 的方法，因为它们依赖于以某种方式构建**文档**，而不是真正地构建**操作**。这意味着在大多数情况下，使用 CRDT 方法，文档将以不同的格式存储，而不仅仅是一个简单的字符串。这包括使用像二叉树和特殊位置标识符这样的东西。

### 对 CRDTs 的不利影响

当使用 CRDTs 代替 OT 时，我们是在用时间复杂度换取空间复杂度。这意味着虽然 OT 比 crdt 花费更多的时间来处理，但 crdt 通常会比 OT 使用更多的内存。

## [标志](#logoot)

Logoot 是 CRDT 的一个很好(也很简单)的例子。它是比较容易理解的 CRDTs 之一，扩展/变体也建立在它的基础上，以支持额外的功能，比如组撤销/重做。这是我将关注的主要 CRDT(咄，看标题)，并且实现它，我将在下一个故事中谈论它。

### 基本原则

回到带有竞争条件的原始示例，您可以看到文档失去了同步，因为操作使用了字符串中的一个位置。因此，当插入发生时，插入字符之后的所有内容都向右移动，这意味着它们的位置增加。

[![The position of the ‘c’ is incremented, from 2 to 3](img/7bbacf88b58e4535b5e7a1b96428ef7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AF7m0naP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/muig2rolgvsax5ld9iji.png)

当对两个内容应用第二个操作时，它们被插入到操作中指定的相同位置。但是，作为第一次操作的结果，它们出现在不同的字符处。例如，在下一个图像中，原意是在“c”后面插入一个“y”。但是，由于在固定位置插入的操作,“y”被插入到“x”之后。

[![The effect of the operation is no longer as intended](img/46dd29bdc2bc04bfae039bb558da3a4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C0SFmyPG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aksug90umvefgv4zd0c7.png)

Logoot 试图通过让位置随文本移动来解决这个问题。也就是说，它为文档中的每个字符/字符组分配唯一的标识符，并根据这些标识符的位置执行插入和删除。

在 Logoot 中，文档被分成文本组。一组文本称为一个`atom`。每个原子都有一个唯一的标识符，或者说是一个`uid`，它必须以一种特殊的方式生成，这将在后面介绍。每一辆`uid`都比得上其他的`uids`。这意味着你可以做类似询问`uid1` < `uid2`的事情，反之亦然。你也可以检查它们是否相等。然后，文档被存储为一个由`(uid, atom)`对组成的数组，由对中的`uids`按升序
排序。每个文档中还会有两个特殊的空行。一个在最开始，有`MIN_uid`，一个在最底部，有`MAX_uid`。

唷，这可真让人受不了。最重要的部分来了。*对于任何两个不同的`uids`，你应该总能在它们之间找到一个`uid`。* *这是让 Logoot 工作的属性。*因此，Logoot 中的`uids`需要以符合属性的方式来表示。

### 代表 uid

让我们从简单的开始。在这段时间里，我们用每个字符在初始字符串中的位置来表示它们的标识符——一个整数。但是，使用整数作为`uids`并不符合我们提到的属性。假设我们有`uids` 4 和 5。不可能找到介于 4 和 5 之间的整数，因此整数不能用作`uids`。

此时，您可能会想，浮动呢？现在，从理论上讲，使用实数作为`uids`是可行的，因为在任何两个不同的实数之间都有无穷多个实数。然而，在计算机环境中，这是不可能的。浮点数只是实数的近似值，这意味着不可能总是在任意两个浮点数之间找到一个浮点数。简单地说，*你不能仅仅用 32 或 64 位的*来表示无限多的浮点数。

那么这些`uids`是如何表示的*？答案是*列表*。使用列表，我们可以很容易地坚持属性。一个`uid`由一列整数表示。假设我们有`uids` `[4]`和`[5]`(注意这些是单个整数元素的列表，而不仅仅是整数)。为了在`[4]`和`[5]`之间找到一个`uid`，因为在`[4]`和`[5]`之间没有整数，我们可以简单地制作一个更长的列表，以`4`开始(例如`[4, 8]`)。现在，我们有了`uids`、`[4]`、`[4, 8]`和`[5]`。这可以永远重复，因为列表是无限可扩展的(当然，列表的最大大小受可用 RAM 的限制，但这已经足够大了，我们不必担心。*

因此，在 Logoot 中，`uids`由整数列表表示。*注意，组成`uid`还有一些额外的组件，但是为了
理解 Logoot，它们不是必需的。*

现在我们已经在 logout 中介绍了`uids`，我们可以继续构建一个简单的 logout 文档。

### 一个样本文档

在 Logoot 中，一个文档由一组`pairs`组成。每个`pair`包含一个`uid`和一个`atom`。一个`atom`就是一个字符串，包含文档中的一些内容。文档中的`pairs`总是按照它们的`uids`排序。要获得整个文档的内容，只需按顺序连接所有的`atoms`。与所有数据结构一样(记住 Logoot 是一个 CRDT)，可以对它执行一些操作。

可以对 Logoot 文档执行`insert`操作。要执行一个`insert`，你只需要有`pair`来插入。要为`pair`生成`uid`，您只需获取两个现有的`uids`，并在它们之间生成一个随机的`uid`。

也可以执行`delete`操作。要执行一个`deletion`，你只需要知道要删除的`uid`。

### 开始和结束行

在`insert`操作中，您可以看到，要生成一个新的`uid`，您需要 2 个现有的`uids`。但是，在一个空白文档中，如何找到现有的两个`uids`？这就是开始线和结束线发挥作用的地方。开始行和结束行是 Logoot 文档中*始终*存在的两行。它们包含一个空的`atom`，它们的`uids`分别是`0`和`MAX`，其中`MAX`是一个整数的最大长度。

## 结论

希望这篇文章已经让您对 Logoot 有了很好的理解，甚至只是一般的协作编辑。我[用 Go 编程语言实现了 logout](https://github.com/ravernkoh/logoot)。看看这里。一定要看看我的其他故事，感谢阅读！