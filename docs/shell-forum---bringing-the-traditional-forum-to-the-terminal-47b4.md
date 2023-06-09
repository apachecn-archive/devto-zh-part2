# 贝壳论坛——将传统论坛带到终端

> 原文：<https://dev.to/mikkel1156/shell-forum---bringing-the-traditional-forum-to-the-terminal-47b4>

有时候你只是想尝试一些新的不同的东西。如果你曾经使用过 BBS(不是说我有-不是我的时间),它可以与之相比，但这里你有一个完整的 Linux 终端。这个项目是我试图创造一个新的平台，与他人分享想法，许多人喜欢这个终端，并尽可能多地使用它。Shell-Forum 是一个项目，旨在通过成为完整终端形式的论坛来满足终端用户的兴趣。

# 为什么？

这只是我的一个随机想法，它是从研究如何制作一个外壳开始的，发现[斯蒂芬·布伦南关于如何用 C 语言制作一个外壳的教程](https://brennan.io/2015/01/16/write-a-shell-in-c/)在我的脑海里翻转了一下。所以这个项目是为了满足我想要构建类似的东西，它也帮助我学习 c。

# *上班怎么样*

没有数据库，而是简单地使用文件结构和目录以明文形式存储数据。

每个用户都有他们自己的主目录，他们可以在那里发帖和工作，其他用户也可以在那里亲自看到这个人写了什么，但当然不能做任何修改。

当用户准备将帖子发布到论坛时，他们使用 commit 命令。提交将显示用户可以提交的所有论坛的列表。当他们选择后，他们只需提交，它将被复制到论坛中，并将其更改为帖子的所有者。

使用搜索命令，用户还可以使用关键字来查找论坛中的帖子。

此外，还将创建另外两个命令，`view`和`comment`。*视图*命令将给出一个更加可视化的文章输出，用 markdown 对其进行格式化，并显示任何和所有评论。*注释*命令将被用来创建一个命令。

# 待办事项

*   添加`view`命令，以 markdown 格式显示格式化的文章，并在最后显示评论。
*   添加`coment`命令来回复一个帖子，评论会放在一个像这样命名的目录里`.comments-<post_name>`
*   搜索“菜单”
*   最佳化
*   其他改进和修复