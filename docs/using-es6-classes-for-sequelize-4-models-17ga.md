# 将 ES6 类用于 Sequelize 4 模型

> 原文：<https://dev.to/hugo__df/using-es6-classes-for-sequelize-4-models-17ga>

ES2015 或 ES6 规范将`class`引入了 JavaScript。
像 React 这样的库从`React.createClass`发展到了`class MyComponent extends React.Component`，ie 从滚动自己的构造器发展到利用一种内置语言来传达程序员的意图。

对于 Node.js web 应用程序的持久层，我想到了一些数据库，比如 [MongoDB](https://www.mongodb.com/) (可能与[mongose](http://mongoosejs.com/)配对)，或者像 [Redis](https://redis.io/) 这样的键值存储。

要用节点应用程序运行关系数据库， [Sequelize](http://docs.sequelizejs.com/) “一个用于 Node.js 的易于使用的多 SQL 方言 ORM”是一个不错的选择。它允许应用程序在 MySQL 或 PostgreSQL 实例的支持下运行，并提供了一种从数据库中的实体表示到 JavaScript 的简单映射方式，反之亦然。

Sequelize 用于模型定义的 API 如下所示(来自 docs[http://docs . sequelize js . com/manual/tutorial/upgrade-to-v4 . html](http://docs.sequelizejs.com/manual/tutorial/upgrade-to-v4.html)):

```
const MyModel = sequelize.define("MyModel", {
  // fields and methods
}); 
```

要添加类和实例方法，您需要编写以下代码:

```
// Class Method
MyModel.associate = function (models) {};
// Instance Method
MyModel.prototype.someMethod = function () {..} 
```

这在 ES6 之前是必要的，因为没有经典继承的概念。既然我们现在有课，为什么不利用它们呢？对于习惯于使用类的开发人员来说，下面这些可能看起来很熟悉:

```
class MyModel extends Sequelize.Model {
  static associate(models) {}
  someMethod() {}
} 
```

Sequelize 其实是支持这个的，但是文档有点欠缺。在 GitHub 的一期文章中可以找到这方面的参考资料:[https://github.com/sequelize/sequelize/issues/6524](https://github.com/sequelize/sequelize/issues/6524)。

这里有一个你想做的事情以及如何使用 ES6 类+从`Sequelize.Model`继承来实现它的备忘单:

*   [定义模型上的字段](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#typed-fields)
*   [创建关联](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#assocations)
*   [自定义表名](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#custom-table-name)
*   [自定义型号名称](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#custom-model-name)
*   [换行查询](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#queries)
*   [实例方法](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#instance-methods)
*   [初始化所有模型](https://codewithhugo.com/using-es6-classes-for-sequelize-4-models/#initialise-all)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

# 用键入的字段初始化模型

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  static init(sequelize, DataTypes) {
    return super.init(
      {
        myField: DataTypes.STRING
      },
      { sequelize }
    );
  }
} 
```

# 将您的模型关联到其他模型

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  static associate(models) {
    this.myAssociation = this.belongsTo(models.OtherModel);
    // or
    this.myAssociation = models.MyModel.belongsTo(models.OtherModel);
  }
} 
```

# 为你的模型设置一个自定义表名

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  static init(sequelize, DataTypes) {
    return super.init(
      {
        // field definitions
      },
      {
        tableName: "myModels",
        sequelize
      }
    );
  }
} 
```

# 为您的模型设置自定义模型名称(用于序列化)

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  static init(sequelize, DataTypes) {
    return super.init(
      {
        // field definitions
      },
      {
        modelName: "myModel",
        sequelize
      }
    );
  }
} 
```

# 换行查询

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  static getId(where) {
    return this.findOne({
      where,
      attributes: ["id"],
      order: [["createdAt", "DESC"]]
    });
  }
} 
```

# 实例方法

```
const Sequelize = require("sequelize");
class MyModel extends Sequelize.Model {
  getFullName() {
    return `${this.firstName}  ${this.lastName}`;
  }
} 
```

# 初始化您的所有模型

`require()`后面跟着`model.init()`是`sequelize.import(path)`的替代，更清楚什么是进口的，什么不是进口的，以什么名义进口。

```
const Sequelize = require("sequelize");
const sequelize = new Sequelize();
// pass your sequelize config here

const FirstModel = require("./first-model");
const SecondModel = require("./second-model");
const ThirdModel = require("./third-model");

const models = {
  First: FirstModel.init(sequelize, Sequelize),
  Second: SecondModel.init(sequelize, Sequelize),
  Third: ThirdModel.init(sequelize, Sequelize)
};

// Run `.associate` if it exists,
// ie create relationships in the ORM
Object.values(models)
  .filter(model => typeof model.associate === "function")
  .forEach(model => model.associate(models));

const db = {
  ...models,
  sequelize
};

module.exports = db; 
```

关于以这种方式使用 Sequelize 或开发由关系数据库支持的节点应用程序的任何问题，请随时在下面评论或发推文给我 [@hugo__df](https://twitter.com/hugo__df) 。

由[尤金·林](https://unsplash.com/photos/sorCkbu4Nzw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/square?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。