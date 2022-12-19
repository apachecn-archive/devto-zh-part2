# Sequelize.js 和 Mongoose.js 的相似之处

> 原文：<https://dev.to/pomyo/similarities-of-sequelizejs-and-mongoosejs-i8i>

选择正确的 ORM 来很好地处理数据库是很重要的。当试图正确有效地持久存储数据时，它可以节省您大量的精力。随着 MongoDB 等 NoSql 数据库的出现，Mongoose 等流行的 ORM 有助于弥合前端和后端数据之间的差距，而 MySql 等传统关系数据库则充分利用了 Sequelize。

虽然 MySql 和 MongoDB 是非常不同的数据库，但通过创建、查找、更新和删除数据来支持它们的 ORM 即使不相同，也非常相似。

这里有一些他们看起来非常相似的地方。

为了查找或选择数据，Mongoose 和 Sequelize 都使用了`.find()`函数。为了返回表中的所有内容，Mongoose 使用`.find({})`，而 Sequelize 使用`.findAll({})`。通过主键或 id 进行查找，Mongoose 使用`.findById()`，Sequelize 使用`.findByPk()`。

对于创建文档或行，Mongoose 和 Sequelize 都使用相同的`.create()`函数来生成新文档或向表中添加新行。两者都有通过针对 Mongoose 的`.bulkWrite()`和针对 Sequelize 的`.bulkCreate()`向各自的数据库发送多个插入的功能。猫鼬也有额外的方法通过`.createCollection()`来创建收藏。

为了更新数据，两种 ORM 都提供了一个`.update()`函数，略有不同的是 Sequelize 声明它自己的`.upsert()`(如果找不到要更新的内容，则插入)函数，而在 Mongoose 中，可以在`.update()`上指定一个选项。猫鼬反过来也有几种不同的更新功能，如`.updateMany()`和`.updateOne()`。

最后，为了删除东西，Sequelize 提供强大的`.destroy()`函数，而 Mongoose 使用`.delete()`(最初也是`.destroy()`)。猫鼬也可以`.deleteOne()`和`.deleteMany()`。