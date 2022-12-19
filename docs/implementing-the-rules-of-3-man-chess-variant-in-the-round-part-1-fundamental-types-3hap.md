# 执行三人国际象棋的规则:变体“回合”。第 1 部分，基本类型

> 原文：<https://dev.to/mkf/implementing-the-rules-of-3-man-chess-variant-in-the-round-part-1-fundamental-types-3hap>

*本帖原载于 2018 年 4 月 Medium 上。里面描述的项目是**暂时**停产。这个帖子甚至没有更新项目。我只是在试媒→Dev。迁移功能。*

* * *

在过去的三年里，我学习新编程语言和范式的首要项目是实现 [3 人国际象棋:变种“回合”](http://3manchess.com)的规则。我用它了解了很多关于 Go、Dart、Java、Clojure 和 Haskell 的知识。所有的实现尝试都可以在我的 Github 档案中找到。“尝试”这个词有什么问题，为什么没有一个有效，有什么困难？这就是这个系列将要讲述的内容。但是在这一部分的“试验”文章中，这些只是基本的数据类型，因为我没有太多的时间。我有这个项目的节日演示截止日期。其中之一是今天的**，四小时后。**所以，如果你能看到全部代码并帮助我，请吧。【https://github.com/ArchieT/ThreeManChess T4】

首先，董事会。它是一个圆形的棋盘，由六个环组成，后来被称为等级，编号从 0(最外层)到 5(最内层)。

```
data Rank = MostOuter | SecondOuter | MiddleOuter 
 | MiddleInner | SecondInner | MostInner 
 deriving (Eq, Ord, Read, Show)
ranks :: [Rank]
inw :: Rank -> Rank
out :: Rank -> Maybe Rank 
```

每个级别由 24 个文件组成。

棋盘分成三份，每个玩家一份。对手的车彼此相邻，但由于一种叫做“护城河”的特征，它们无法捕获对方。卒也不能，因为有一个叫“小溪”的特征。如果你想知道它看起来怎么样或者有其他问题，[www.3manchess.com](http://www.3manchess.com)可能会回答他们。

> 1.  Put all kings on white. First it's white, then clockwise it's gray, then black.

```
data Color = White | Gray | Black
 deriving (Eq, Ord, Read)
next :: Color -> Color
prev :: Color -> Color 
```

所以现在我们也需要一些数据类型，对应于从 0 到 7 的数字，为什么不使用整数类型呢？因为我们希望受到 Haskellish 类型系统的保护，而我们处理这种数据类型的另一种方式可能在某些方面对我们有利。所以。向
问好

```
data SegmentHalf = FirstHalf | SecondHalf 
 deriving (Eq, Ord, Read, Show)
data SegmentQuarter = SegmentQuarter 
 { half :: SegmentHalf
 , halfQuarter :: SegmentHalf }
 deriving (Eq, Ord, Read, Show)
data SegmentEight = SegmentEight 
 { segmentQuarter :: SegmentQuarter
 , quarterHalf :: SegmentHalf } 
 deriving (Eq, Ord, Read, Show)
type ColorSegment = Color
data File = File { segmColor :: ColorSegment
 , colorSegmFile :: SegmentEight }
 deriving (Eq, Ord, Read, Show)
opposite :: File -> File
plus :: File -> File
minus :: File -> File

-- and finally
type Pos = (Rank, File)
rank :: Pos -> Rank
file :: Pos -> File

-- |'kfm' is where the King stands
kfm :: SegmentEight
kfm = (SegmentEight (SegmentQuarter SecondHalf FirstHalf) FirstHalf) 
```

好了，板坐标数据结构说得够多了。

> 1.  棋盘的中心可以通过，但是没有正方形可以占据。
> 
> a.穿过中心的相邻垂直方块保持相同的颜色。
> 
> I .王后、国王、车或兵，通过中心前进 1 格，保持相同颜色。
> 
> 二。骑士也将保留同样的颜色。
> 
> 三。这种现象对于保持“圆板”的完整性是必要的。玩家应该意识到这一点，传统的国际象棋策略是不会妥协的。
> 
> […]
> 
> 1.  垂直移动(皇后、国王、车、兵)
> 
> a.沿着棋盘文件直线前进(如有必要，穿过中心)。

棋子只能往前走。所以，我们不想证明一个棋子不可能进行如此多的捕捉，以至于不确定它是向内还是向外，也不确定我是否已经证明过这是不可能的，但无论如何，这种区分是有帮助的。因此，

```
data FigType = InwardPawn | OutwardPawn | Rook | Knight
 | Bishop | King | Queen
 deriving (Eq, Read)

--for promotion purposes, we will also define
data Promotion = RookPromotion | KnightPromotion |
 | BishopPromotion | QueenPromotion
 deriving (Eq, Show)
desiredType :: Promotion -> FigType 
```

> 1.  水平移动(皇后、国王、车)
> 
> a.围绕同一行(等级)的中心旋转。

所以这步棋就像模 24。

> 1.  对角线移动(王后、国王、主教或卒捕捉)
> 
> a.沿着 2 条轨迹线中的一条从它所在的正方形出来。

在棋盘上，有 48 条对角线*轨迹线*，它们成对地穿过每一个方格。在最外面的一排，在每个锉刀上，一对*轨迹线*形成一个角。每一对都在中心形成一个环。

> b.可以通过中心旋转。
> 
> c.不能在 1 步之内在外排“转弯”。

此外，在我们的通信中，当我特别要求并为此进行辩论时，三人国际象棋的所有者克里夫·金先生(我猜是他们的创造者)说什么是正确的事情，而规则中没有包括什么， 图形不能斜着穿过中心或横着绕着中心回到它原来所在的方块——这会导致缺乏“强迫移动”(国际象棋的一个重要特征),并会在游戏中产生严重的后果，特别是在电脑游戏中。

> 1.  骑士移动
> 
> a.垂直 2 个方块，然后水平 1 个方块，或
> 
> b.垂直 1 个正方形，然后水平 2 个正方形。
> 
> c.使用上述两种方法中的一种(即，不要沿着穿过中心的轨迹线)。此外，这两种方法对于下面描述的边界控制是必要的。

这些都差不多，直到谈到“边境管制”，即“护城河”。

因此，有多种方法来执行单个 from→to move，我们需要分别处理这些方法以避免不必要的计算。因此，我们需要那个
的数据结构

```
data Orientation = Rankwise | Filewise deriving (Eq, Read, Show)
data RankwiseDirection = Inwards | Outwards
 deriving (Eq, Show)
data FilewiseDirection = Pluswards | Minuswards
 deriving (Eq, Show)
filewiseInc :: FilewiseDirection -> File -> File

data DiagonalDirection = DiagonalDirection
 RankwiseDirection
 FilewiseDirection
 deriving (Eq, Show)
data Count = Once | OnceMore Count deriving (Eq, Read, Show)

class (Eq a) => LinearDirection a where
 addOne :: a -> Pos -> Maybe Pos
instance LinearDirection RankwiseDirection where
 addOne Inwards (MostInner, file) = 
 Just (MostInner, opposite file)
 addOne Inwards (rank, file) = 
 Just (inw rank, file)
 addOne Outwards (rank, file) =
 do { o <- out rank;
 return (o, file) }
instance LinearDirection FilewiseDirection where
 addOne w (rank, file) = Just (rank, filewiseInc w file)
instance LinearDirection DiagonalDirection where
 addOne (DiagonalDirection Inwards p)
 (MostInner, file) = ... --long case expressions here
 addOne (DiagonalDirection Inwards p) (rank, file) = 
 Just (inw rank, filewiseInc p file)
 addOne (DiagonalDirection Outwards p) (rank, file) = 
 do { o <- out rank; return (o, filewiseInc p file) } 
```

我现在要回去工作了，我不确定我是否有充分的理由不发表这个开头。到目前为止，这篇文章几乎没有什么困难，尽管即使是这几条警告也让我花了很多时间去思考。我写这篇文章是为了激励自己，阐明我的代码的基本原理。我希望有合作者。真的。**任何。**任何**的合作者。**你不需要了解 Haskell 超过几个小时，**你会一路学习 Haskell**。

所以。[**https://github.com/ArchieT/ThreeManChess**](https://github.com/ArchieT/ThreeManChess)

 **如果你想知道更多关于“为什么白嘴鸦不互相捕捉”或“这一切看起来像什么”的信息，看看[www.3manchess.com](http://www.3manchess.com)。

我应该稍后更新这篇文章还是只发布下一部分？也许张贴下一部分会更好。**