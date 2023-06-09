# Gostars -创建明星名字

> 原文：<https://dev.to/dnnrly/gostars-creating-star-names-1geh>

当我们想要创建一个新的 Gostars 游戏时，我们想要输入最小的参数。具体来说，我们想创建一个游戏，设定宇宙的大小和恒星的密度，而不是单个恒星的名字。

那么我们如何从头开始生成可读的名称呢？

# 生成人名

## 随机字符串

让我们尝试创建完全随机的字符串。从 26 个字母开始，随机挑选，直到你厌倦为止。这里有一些代码可以做到这一点，为随机大小的单词随机选择字母。试试看[这里](https://goplay.space/#saj4F_db44k)。

以下是我生成的一些单词:

```
Pthc
Dnlbn
Ugfb
Vndmm
Ikvji
Leafd
Krxpzaelp
Ldwnps
Pmcnaxr
Rcucryh 
```

这些确实看起来像外星人，也许有点太多了！`Leafd`和`Krxpzaelp`看起来足够体面，但我不知道我能发出其余的任何声音。这对我们的玩家来说似乎不太友好。

## 随机音素

如果我们可以通过随机选择“声音”来生成英语发音单词，那会怎么样。我指的是[字素](https://www.phonicbooks.co.uk/2011/09/03/what-is-a-grapheme/)。好主意，让我们试试——但是找到一个完整的字素列表是一件*痛苦*的事情！经过大量的寻找，我最终发现最好的资源是[这个](http://www.dyslexia-reading-well.com/44-phonemes-in-english.html)。

下一个要使用的工具类似于随机选择字母。给定一组字形，随机选择若干次。做这件事的代码是这里的。您需要提供一个每行包含一个字素的数据文件。像这样运行:

```
go run graphemetxt.go -source data.txt 
```

我们来看看都生成了什么样的名字:

```
guskngcklff
nbquwhcggrch
lkghged
seghssghcc
btgufflkghst
rgschdpn
jzgzwhsssfgh
lfzzrpsggguked
sejscftbbgezfpn
gueuthss 
```

我不确定这有什么好处。

## 马尔可夫发生器

在这里，我们将拿出大枪。[马尔可夫链](https://en.wikipedia.org/wiki/Markov_chain)查看现有的数据体，并根据其跟随当前状态的概率随机选择下一个“状态”。在我们的例子中，每个字母代表一个州。

在互联网上快速搜索发现了一个方便的小[库](https://github.com/unixpickle/markovchain)，它将在这里帮助我们。[这里的](https://github.com/dnnrly/gostars/blob/feature/star-names/tools/textgen/textgen.go)是一个程序，它将读入一个每行有一个新名称的数据文件，以建立一个如何构造名称的模型。要使用它，请在命令行中键入以下内容:

```
go run tools/textgen/textgen.go -source <your data file> 
```

我们需要一些源数据来构建马尔可夫链。为此我搜索了英国的地名，我选择了一个英语名词和地名的列表(县和城镇)。只要地名每行有 1 个，它就会作为我们的数据。

您可以使用`words`参数设置字数。还有另一个参数，即“前缀”。这用于设置当决定下一个选择哪个字母(州)时，算法在链中“向后”看多远。我还在学习这是如何影响输出的，但是使用`2`给出了一些有趣的输出:

```
Implain-landear
Adamod
Mouningendroold
Anchal
Olelughtemplail
Ariabervaging
Hdrack
Entivult
Rimbe
Usholynx 
```

那就*好多了*！相当陌生，但不知何故可读。

该工具的一些有趣特性是:

*   当我们遇到换行符时，它是单词的结尾，所以在链中选择一个新的位置
*   我们通过将原始单词添加到一组已知单词中来过滤掉它们
*   重复的单词也可以通过存储在我们的集合中来删除

使用`map`键对器械包来说效果很好。

# 调遣人名

现在我们有一个很大的名单，我们如何让他们加入我们的游戏？这就是我们利用 T2 水牛 T3 的优势。它包括一个工具`packr`，允许你将静态资源嵌入到用`buffalo build`生成的二进制文件中。所以首先，我们需要产生一个名单(你可以在这里找到他们)。那么我们如何让它们对程序可用呢？

让我们从我们需要名字的时候如何使用它们开始。Gostars 的第一版 cut 在你创建游戏的时候就创造了新星。当客户端对`/games`执行`POST`操作时，处理程序会查看请求字段中定义的宇宙的大小和恒星密度，并在随机位置生成随机数量的恒星。当我们生成星星时，我们需要从我们的列表中寻找一个名字，最好是随机的。否则每场比赛都会有相同的明星名字。我们如何解决这个问题？好吧，原来 Go 有解决这个的办法。当你在地图键上`range`时，Go 会以随机的顺序显示它们。为了尽可能地简化我们的代码，我认为我们需要提供一种类型，让我们从映射键中获得任意数量的这些名称。看看 [`names.go`](https://github.com/dnnrly/gostars/blob/feature/star-names/actions/names.go) 就知道是哪种类型了。

下一个问题是，我们如何填充我们的`nameList`？当你写一个 Buffalo 应用程序时，你不要跳到`main`，你作为开发者的切入点是`actions.App()`。这是所有资源初始化发生的地方。我们需要创建一个`nameList`类型的包变量，并在`App()`中实例化它，传入一个包含我们之前创建的文件中所有数据的字符串。为此，我们创建了一个引用 repo 中的`data`目录的`packr.Box`。当我们运行`buffalo build`时，`packr`工具将扫描源代码，检测我们调用`packr.NewBox(...)`的位置，并将该目录的内容作为数据存储在二进制文件中。要访问这个目录中的文件，我们需要在存储它们的盒子中查找它们。对于我们的明星名字，我们只需从盒子中查找`starnames.txt`，并将这个字符串传递给我们的`nameList`。

当我们创建星星时，我们可以访问由我们的马尔可夫链工具生成的静态数据，并且在需要时可以快速利用它们。

希望这有用。