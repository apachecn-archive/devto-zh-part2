# MongoDB——一种不那么愚蠢的推送方式

> 原文：<https://dev.to/vblaha/mongodb-a-less-goofy-way-to-push-29nl>

# MongoDB——一种不那么愚蠢的推送方式

[![skating](img/2b1338ef482acf40b6ba0860b00172df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rcptve3e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.cheffamily.com/wp2015/wp-content/uploads/2015/01/Mongo.png)

任何有滑冰背景的人听到这个词都会打转。蒙哥。令人欣慰的是，技术开发人员已经设法收回了这个词，并给予它一些应有的尊重。MongoDB 的历史就像滑板运动一样曲折。致力于开发名为 Babble 的开源平台(类似于当今的 Google Apps)的开发人员必须建立自己的数据库来适应他们的项目。Babble 没有足够的用户兴趣，但 MongoDB(humongous 的缩写)用 flex 填补了数据库模型的空白，只需做最小的更改即可按需扩展。有了这段历史，就更容易理解像 Mongoose 这样的其他分裂程序是如何从 MongoDB 中抽取并增强其属性的。Mongo 易于使用 Javascript 符号，并且高度可扩展到不断增长的数据，使其成为 MERN 堆栈(Mongo、Express、React 和 Node)的关键组件。因此，让我们仔细看看您可以使用 dat DB 的一些方法！

MongoDB 不使用查询结构——相反，数据存储在 BSON(二进制 JSON 格式)中。使用 Mongo，一台服务器可以保存多个数据库，在数据库中存储集合。集合有点像保存多个文档的文件夹(一个文档是一条数据记录)。每个数据库可以有多个集合，每个集合可以有多个文档。Mongo 将这些集合构建为对象。

存储之后，Mongo 会为每个文档分配一个惟一的 ObjectID。MongoDB 将创建它被告知的任何东西，经常产生副本或其他意外的错误信息，因为 MongoDB 是在命令行中运行的。MongoDB 使用两个独立的终端窗口，一个作为外壳(mongod)，一个作为客户端(mongo)。Mongo 接收文件并为我们管理它们。我们使用客户机 mongo 通过查询或我们给 mongo 的命令与 shell(mongod)通信。

用户信息:
{
姓名:【维罗妮卡】，
年龄:【32】，
身份:【女王】，
组别:【“3 个阿米加斯”、“编码训练营”、“GDI”]
}

现在让我们组建队伍...
{
姓名:【贝尔特里兹】，
年龄:【29】，
身份:【女王】，
团体:【“3 阿米加斯”、“慕克地”、“GDI”】
}

{姓名:【索菲亚】，
年龄:【28】，
身份:【女王】，
组别:【“3 阿米加斯”、“GDI”、“MOOC land”】
}

现在，我们已经构建了一个文档集合来存储在我们的数据库中，并带有一个对象 id。我们可以通过我们的第二个终端窗口 mongo 调用这些对象并操纵数据。

虽然 Mongo 可以在 shell 中编写，但这很繁琐，而且在没有 UI 的情况下将不同的集合连接在一起也很困难，所以在 MongoDB 中使用 ODM(对象文档映射器)可以扩展功能，并减少管理数据库时令人头痛的因素。这就是我们简单、漂亮的 DB，它一点也不像丑陋的 skate scoot，但可以说没有代码编辑器也一样痛苦。

查看我在 Mongoose 和客户端 ui 上的帖子[这里](https://dev.to/vblaha/mongoose-5ag4)了解更多，感谢观看！我欢迎对扩展和改进的反馈和评论。