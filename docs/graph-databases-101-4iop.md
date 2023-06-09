# 图表数据库 101

> 原文：<https://dev.to/jerodsanto/graph-databases-101-4iop>

[Dgraph](https://dgraph.io/) 创始人 [Manish R Jain](https://twitter.com/manishrjain) 在 Changelog 的[第 322 集给我们做了一个*精彩的*图形数据库讲解。](https://changelog.com/podcast/322)

*(感谢听众[埃文·吉纳斯特](https://twitter.com/MisterGenest)在推特上强调这一事实[。)](https://twitter.com/MisterGenest/status/1063224954446069761)*

我从我们可靠的[集文字稿](https://changelog.com/podcast/322#transcript-34)中摘录了这一段，以飨读者:

> 我认为图表可能是最简单的东西，真的。人们会想到 SQL 表——有一行，有几列。把一个图想象成三列，分别是主语、谓语和宾语。如果你把这些东西放在一起，你会得到一个图表。主体本质上是——把它想象成一个实体；谓词是关系，对象是另一个实体或值。
> 
> 比方说，主题可以是我。关系可能是“住在”,对象可能是“旧金山”也可能是我——名字叫 Manish，这有点像一种财产。所以你只要把所有这些事实，或者三元组放在一起，你就会得到一个图表。
> 
> 那么其他住在旧金山的人也会有类似的事实，然后你可以运行一个图形查询“嘿，告诉我所有住在旧金山并且吃寿司的人。”所以你挑选了所有住在旧金山的人，你和世界上吃寿司的人有交集——这是完全不同的事实。你没有把他们创造成“这个人住在旧金山，吃寿司。”这是我们正在做的事情。所以你选择旧金山的所有人，你选择世界上所有吃寿司的人，你将两个列表相交，现在你得到旧金山吃寿司的人。现在你可以得到这个结果，然后说“把它和所有去过日本的人进行交叉。”你拿起另一个去过日本的人的名单，和这个相交，现在你得到住在旧金山，吃寿司，和去过日本的人。
> 
> 所以图的力量真的在于你可以做的这些连接，来自非常简单的事实。

从 30:09 开始亲自聆听[或从头开始:](https://changelog.com/podcast/322#t=30:09)

 [# 322:去而复返(拉斐尔的故事)](/changelog/322-there-and-back-again-dgraphs-tale)  [## 变更日志](/changelog) ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png) ![The Changelog](img/33d4471b5fdda875ad9b5ed198e40b23.png)

<audio id="audio" data-episode="322-there-and-back-again-dgraphs-tale" data-podcast="changelog"><source src="https://cdn.changelog.com/uploads/podcast/322/the-changelog-322.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![322: There and back again (Dgraph's tale)](img/d2125475d49febff157a113ea9ac7d15.png) ![animated volume bars](img/b4c6951c950232cdfef32ac750ed6d92.png) ](/changelog/322-there-and-back-again-dgraphs-tale)  ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png)     ![volume](img/64c7607b77476eb3d924254262a5bca4.png)   <input type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![mute](img/53eef5aa80ab0610ec1ea90b95d54171.png)  1x  initializing... × 

谢谢，曼尼什！💚💚💚