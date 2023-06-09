# 使用 G-Script 的文本到表情转换器

> 原文：<https://dev.to/skrish2017/text-to-emoji-converter-using-g-script-4ln6>

做了一些我觉得有趣的事。所以分享到这里。如果这里有老师的话，他们可以想出一些方法，以此为模型，向学生教授不同的编码概念。

我设计了一个小的 Google 脚本代码，它接受纯文本并将其转换为表情符号文本。基本步骤如下。

步骤 1:设置 Google 表单。

我有 2 张床单。一个用于输入/生成的输出，另一个用于具有单词>等效表情符号的查找。第二张工作表是按单词排序的，因此可以实现二分搜索法搜索。

第一张
[![Sheet 1](img/13f2381b26faf58ac65a409df77751f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzEOz5dV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2h9qsy47efzode2vz8mw.jpg)

第二张
[![Sheet 2](img/ea9e0766b6681e127ce87c2ce5c62731.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bsrl-LU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gbhpg0johdbbbqwfksl8.jpg)

第二步:代码

该代码由两个函数组成。

convertTexttoEmoji():这是分析输入文本并输出转换后的表情文本的主函数。getEmoji():这个函数由 convertTexttoEmoji()调用，负责为每个单词查找匹配的表情图标。这个函数在这个操作中使用了二分搜索法。

转换文字 tomoji()
[![convertTexttoEmoji](img/601218e1cfd1c28949295ae11bc3bf79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I1lLgZlh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lxcrsbalxg0k93hv1k3l.jpg)

getEmoji()
[![getEmoji()](img/59d6c3a9dcd952d73013bd7a40d0ba53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MkbhuGdw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/swaibhtugavjm4swphjw.jpg)

第三步:结果

[![Output 1](img/30f5a744ec2165d3642b69bb141a71b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qd7LDQzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sdzkjffh75v8p2fv25cc.jpg)

[![Output 2](img/0b1fb60f81db2a13cde627e840b9411e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lc9aVYsT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kam3gvrkrhl4wuxbvkgw.jpg)

结论:

*   被查看的单词是绝对匹配的(包括区分大小写)。因此，如果我键入“自行车”而不是“骑自行车”,它不会工作。解决方案？用逗号将更多的单词添加到数据集中，并对相似的单词进行二级查找+强制小写以进行一致性检查。

*   将这个文本拆分成一个数组很容易，但是由于空格的原因，在输出中处理标点符号有点棘手。

*   表情数据库本身是有限的，因此更大的数据集可能需要更长的时间来运行/执行。

*   表情符号在不同的设备上看起来不同。因此，一座山在你的设备上可能看起来很完美，而在其他设备上可能看起来像一个丑陋的大三角形。用各种设备测试它是一个好主意！