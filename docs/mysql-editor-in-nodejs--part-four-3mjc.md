# node.js 第四部分中的 mysql 编辑器

> 原文：<https://dev.to/link2twenty/mysql-editor-in-nodejs--part-four-3mjc>

# MySQL 编辑器中的 NodeJS

这一周我感觉做得不多，我不得不往回走一点，以防止以后出现问题，但我也在查询框中看到了结果表。

如果你想跟随我的旅程，这是我的[帖子索引](https://dev.to/link2twenty/adventures-of-a-hobbyist--index-4oj7)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[ignis-pwa](https://github.com/ignis-pwa)/[node tree](https://github.com/ignis-pwa/nodetree)

### 一个用 NodeJS 写的 MySQL 浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 节点树

一个用 NodeJS 写的 MySQL 浏览器

在 [dev.to](https://dev.to/link2twenty/mysql-editor-in-nodejs--part-one-42j0) 上阅读该项目

试玩一下[演示](http://nodetree-mysql.herokuapp.com/)密码是`demopassword`

</article>

[View on GitHub](https://github.com/ignis-pwa/nodetree)

## 回溯

在我的概念中，事实上在我的最后一次迭代中，有一个文件保存了连接到服务器的所有配置。这包括要连接的默认数据库。

问题出在我的查询代码上，当我连接到一个数据库时，我会查看配置文件来获取数据库的名称。每当您更改正在查看的数据库时，数据库名称就会更新。如果只有一个用户或一个会话在进行，这不会有问题，但是一旦有多个会话，我们就有问题了。

例如，如果用户 A 连接到 test_site 并运行`DELETE * FROM users WHERE username LIKE "a%"`，同时用户 B 连接到 live_site，那么，您可以看到问题。

拆开所有这些，并通过多个会话使其工作，需要一点时间。

## 视觉变化

这周我真的没有做任何视觉上的改变，我添加了表格视图和查询框，但是我只是继续使用和以前一样的设计规范。这里有一张 gif。

[![Loading tables](img/abc5c58a24ac35c962b73f6df4a6e89a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4VhYQekP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yjdl93rrbpfzpyoj3q0d.gif)

## 下一步

我们已经接近项目的尾声了，只剩下两个功能需要实现，当然，我还需要做一些润色和寻找 bug。

*   功能查询框(代码就位只需要实现)
*   添加编辑字段的功能
*   抛光和捕虫

我知道添加编辑字段的功能看起来是一个很大的进步，但我认为这应该很简单。我计划在下一周/下一次进行下两个步骤，如果你们中的任何人愿意帮助我寻找 bug，那就太好了。

## 注销

这是另一篇已经完成的文章，正如我所说的，我感觉自己这周没有取得太大的进步，但是每一步都是朝着正确的方向前进了一步。非常感谢您的阅读。

因为你知道我是谁