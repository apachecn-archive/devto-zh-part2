# Scala 模式匹配(探索不同的解决方案)

> 原文：<https://dev.to/bettyes/scala---pattern-matching-exploring-different-solutions-4j48>

这是一个非常简短的关于 Scala 模式匹配的评论。

我最近不得不在 Scala 中处理一些模式匹配操作。你有没有被所有的`match{ case x+> y}`迷惑过？为了充分理解 scala 中的匹配情况表达式，我尝试使用不同的方法来解决一个简单的模式匹配任务。请在 [github](https://github.com/BettyES/HenryWives) 上找到包括测试套件的完整代码。

任务:**亨利的哪个妻子活了下来？已知名和结婚年数(四舍五入)，人们可以确定亨利的妻子是死了还是在他的婚姻中幸存。**

提醒一下:
**(1)阿拉贡的凯瑟琳，24 岁，幸存于**(1509 年 6 月 11 日-1533 年 5 月 23 日；婚姻无效)；
**②安妮·博林，2 岁，卒**(1533 年 5 月 28 日——1536 年 5 月 17 日；1536 年 5 月 19 日在伦敦塔被斩首。伊丽莎白一世的母亲)；
**(3)珍·西摩尔，1 岁，卒**(1536 年 5 月 30 日——1537 年 10 月 24 日；1537 年 10 月 24 日，分娩后 12 天死于并发症。爱德华六世的母亲)； **(4)克里维斯的安妮，0 岁，幸存**(1540 年 1 月 6 日-1540 年 7 月 9 日；作废)；
**(5)凯瑟琳·霍华德，1 岁，卒**(1540 年 7 月 28 日——1541 年 11 月 23 日；1542 年 2 月 13 日在伦敦塔被斩首)，
**【⑥】凯瑟琳·帕尔 2 年后幸存**(1543 年 7 月 12 日——1547 年 1 月 28 日；丧偶；幸存于亨利八世)

当然，最简单的解决方案如下:

```
(name == "Catherine" && marriage >=2) || (name == "Anne" && marriage <1) 
```

实现匹配大小写表达式，也可以使用以下方法:

```
name match {
     case "Catherine" => marriage match {
         case 1 => false
         case _ => true
     }
     case "Anne" => marriage match {
         case 0 => true
         case _ => false
     }
     case _ => false
 } 
```

然而，case 也适用于 if 语句

```
name match{
     case "Catherine" if marriage >= 2  => true
     case "Anne" if marriage < 1 => true
     case _ => false
 } 
```

最后，有可能使用布尔

```
(name == "Catherine" , name =="Anne" , marriage < 1, marriage >=2) match{
  case (true, false, false, true) => true
  case(false, true, true, false) => true
  case(false, false, true, false) => false
  case(true,false,true, false) => false
  case(false,true, false, true)=> false
  case _ => false
} 
```

如果有人能想到任何其他的解决方案，我很想在评论区看到它们。我渴望提高我的 scala 编码。=)