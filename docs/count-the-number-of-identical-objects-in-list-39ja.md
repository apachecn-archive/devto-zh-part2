# 计算列表[_]中相同对象的数量

> 原文：<https://dev.to/ihhha/count-the-number-of-identical-objects-in-list-39ja>

让我们假设我们有一个对象列表

```
 case class Cake(id: Int, name: String, expired: Long)

val l = List(Cake(1,"keks"), Cake(1,"keks"), Cake(1,"keks"), Cake(4,"pie")) 
```

Enter fullscreen mode Exit fullscreen mode

开始计数

```
l.groupBy(_.id).map(pair => (pair._1, pair._2.length))

// res0: scala.collection.immutable.Map[Int,Int] = Map(4 -> 1, 1 -> 3) 
```

Enter fullscreen mode Exit fullscreen mode

很好，但是如果我们想把所有的对象作为一个键呢

```
l.groupBy(_.id)
  .map({
    case (_, items) => items.head -> items.length
  })

// res1: scala.collection.immutable.Map[Cake,Int] = Map(Cake(4,pie) -> 1, Cake(1,keks) -> 3) 
```

Enter fullscreen mode Exit fullscreen mode

搭配更好。当审查包含此代码的拉式请求时，我收到了同事的评论

不安全所以很糟糕

所以，我们需要改变它

```
l.groupBy(_.id)
  .map({
    case (_, items) => items.headOption.map(_ -> items.length)
  })
// res2: scala.collection.immutable.Iterable[Option[(Cake, Int)]] = List(Some((Cake(4,pie),1)), Some((Cake(1,keks),3))) 
```

Enter fullscreen mode Exit fullscreen mode

然后用`List[Option[_]]`和
进行漂亮的恶作剧

```
l.groupBy(_.id)
  .map({
    case (_, items) => items.headOption.map(_ -> items.length)
  }).flatten

//res3: scala.collection.immutable.Iterable[(Cake, Int)] = List((Cake(4,pie),1), (Cake(1,keks),3)) 
```

Enter fullscreen mode Exit fullscreen mode

点睛之笔

```
l.groupBy(_.id)
  .flatMap({
    case (_, items) => items.headOption.map(_ -> items.length)
  })

//res4: scala.collection.immutable.Iterable[(Cake, Int)] = List((Cake(4,pie),1), (Cake(1,keks),3)) 
```

Enter fullscreen mode Exit fullscreen mode

问题:*你同意我同事的意见吗？*