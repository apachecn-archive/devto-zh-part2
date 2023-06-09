# 猫鼬

> 原文：<https://dev.to/vblaha/mongoose-5ag4>

# 介绍 Mongoose ODM:坚固的框架和紧密的支点！

[![bmx](img/bfc22d2105755be63c77a17c8c3745f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gMYot0_8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bmxunion.com/wp-content/uploads/2017/11/mongoose-bmx-in-japan-video-2017-750x394.jpg) 
如果你是通过搜索小轮车图片找到这个页面的，这里有一段[猫鼬摩托](http://www.mongoose.com/usa/roots/)的有趣历史。如果没有，你仍然读它，那么这是另一个消磨时间的分心，互联网的恭维。专注！在这里，我们将讨论 mongose JS 框架，它已经为 JS 开发人员赢得了自己的名字，作为一种在完整的 JS 堆栈中运行良好的健壮的工具。下图有助于形象化地展示 Mongoose 如何使用我们的 Mongo 客户端和节点循环地(双关语)交付数据库信息。

[![model](img/89bdf4f321617f45d85637fe44232562.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CDQwH_Xy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ap3d4omlez1jwuriod9h.png)

要使用 Mongoose，我们必须安装上图中的大部分项目。Mongoose 被设计为与后台运行的 MongoDB 协同工作，因此建立连接是我们首先要关注的。你可以在这里下载 MongoDB [。
在终端中使用命令:mongod
启动 mongoDB，然后打开第二个终端窗口并运行命令:mongo](https://www.mongodb.com/download-center#atlas)

您还需要下载 Node。JS [这里](https://nodejs.org/en/)。成功安装 Node 之后，使用以下命令安装 mongose 包:NPM install mongose-save

我们必须通过 mongose 创建一个与 MongoDB 的初始连接，最好的方法因操作系统而异，所以这里是对[mongose 文档](https://mongoosejs.com/docs/connections.html)的介绍。

Mongoose 以模式的形式编写数据库集合。模式充当数据集的模板或参数，在前端和后端之间移动信息之前，为集合提供更多的结构、组织和可读性。下面是一个模式的例子:

const mongose = require(' mongose ')；

const UserSchema =新猫鼬。Schema({
用户名:字符串，
邮件:字符串，
唯一:真，
}，{时间戳:真})；

module . exports = mongose . model(' User '，User schema)；

有几种不同的模式类型:

字符串
编号
日期
缓冲区
布尔型
混合型
ObjectId
数组
Decimal128
映射

或者，您可以使用 type:property 方式声明模式类型。使用单词“require”就像一个验证器，告诉我们必须提供该字段才能成功保存文档。[ ]表示一个项目数组，可以对这些数组执行 Javascript 数组方法，比如。推，。pop 等。如果您熟悉 Javascript，您可能会注意到 Mongoose 直观地与 Javascript 一起工作，创建了一个无缝的体验，对新用户也很友好，使其成为一个很好的教学工具。

现在您已经有了一个非常基本的模式，您应该能够将它添加到您的数据库中，并通过安装一个 UI(如 [Robo 3T](https://robomongo.org/download) )来查看它。

通过制作和操纵数据来享受这个游戏的乐趣吧！感谢阅读！