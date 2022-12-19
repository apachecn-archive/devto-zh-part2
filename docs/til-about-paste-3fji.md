# 倾斜至糊状

> 原文：<https://dev.to/lacanlale/til-about-paste-3fji>

在我的大部分工作中，我不得不清理和合并多个数据文件。今天我了解到 bash 有一个简单的命令叫做 paste。下面是联机帮助页上所说的
[![alt-text](../Images/6d54d8a38c4ffdd9b7563d28d4d502dc.png "Man pages")](https://res.cloudinary.com/practicaldev/image/fetch/s--h5NC2GvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ai6xj4zw8w19i6sypqp.png) 
现在有很多东西需要消化，但它有什么用处呢？这里有两个文件，fileone.csv 和 filetwo.csv

[![alt-text](../Images/8057fd740e092e6b312417dea13e6b22.png "fileone.csv")](https://res.cloudinary.com/practicaldev/image/fetch/s--rCAAAKot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8q9mher96md44jsq86ql.png)
[![alt-text](../Images/496f7ba0b71f6aa08f77581523a1aaa7.png "filetwo.csv")T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--R4aVrd-q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cug5t1qm9hixt6d801xm.png)

我通常不喜欢浪费代码行来读取多个数据帧，所以我通常想把它们组合起来。现在，将它们组合起来不仅仅需要简单的`paste fileone.csv filetwo.csv > combined.csv`，因为输出是这样的:
[![alt-text](../Images/00e7203dac8562dd3197783066ea9327.png "bad output")](https://res.cloudinary.com/practicaldev/image/fetch/s--H-2eQcJR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9h380f5p7v70s9m8gu7.png) 
这是因为粘贴合并了文件。没有指定替换字符。对于我的工作，我使用

`paste -d"\n" fileone.csv filetwo.csv > combined.csv`

为了实现我想要的输出:
[![alt-text](../Images/ffd7fb51cf8e60ecd1a002efc44c8f23.png "correct output")](https://res.cloudinary.com/practicaldev/image/fetch/s--YRJHafeI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xiyek3o8stitesitlnrj.png) 
现在我要做的就是删除一行。是的，你可以合并多个 csv 文件，但是你的标题行会被移动到顶部，这使得删除更加容易。*如果您使用维姆:^，则更加轻松)*