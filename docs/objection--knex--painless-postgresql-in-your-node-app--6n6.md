# Objection + Knex =您的节点应用程序中的无痛 PostgreSQL

> 原文：<https://dev.to/aspittel/objection--knex--painless-postgresql-in-your-node-app--6n6>

众所周知，我是一个完全的 PostgreSQL 迷——我很少看到使用不同数据库的用例，尤其是在支持数组和 JSON 字段的情况下。我也喜欢简单 API 的 Node 和 Express(没有 auth)。过去，Node 和 Express 中的 SQL 支持并不完善。我一直听说[异议](https://vincit.github.io/objection.js)很棒，所以我决定尝试一下！

Objection 构建在 [Knex](http://knexjs.org/) 之上，使用新的 ES7 类特性为 Node 构建了一个不错的 ORM 查询语言。ORMs 允许您使用您的应用程序正在使用的任何编程语言来查询数据库，而不是使用数据库的 natie 语言进行查询(这里我们将使用 JavaScript 而不是 SQL 来与我们的数据库进行交互)。由于异议仍然是新的，我将一步一步地检查我的所有代码。

## 学习过程

对于这个项目，我完全依赖于文档。Knex 文档非常棒，Objection GitHub 上的[示例](https://github.com/Vincit/objection.js/tree/master/examples/express-es6)也非常有用。由于我制作了如此多的 Express 应用程序，鉴于我向我的学生广泛教授 Express，在浏览了这些资源后，我对继续这个项目感到非常舒服。

## 最终项目

我一直很难想出这个博客的应用程序创意！于是，我建了一个 app idea app！模型相对简单:`ideas`和`comments`，但是它们仍然展示了异议的最大用例之一:数据之间的关系。`ideas`将是附加了“孩子”注释的“父母”。本质上，用户将能够对各种应用的想法发表评论。

#### Knex 初始化

首先，我初始化了 Knex，它将使用`pg`、我们的迁移和种子来促进我们的数据库连接。在我的[索引文件](https://github.com/aspittel/app-ideas/blob/master/index.js)中设置了典型的 Express API 样板文件，并在我的 [package.json](https://github.com/aspittel/app-ideas/blob/master/package.json) 中安装了需求之后，我在我的项目的根目录中运行了`knex init`。这创建了一个`knexfile.js`,其中包含一个样板文件，带有到数据库的示例连接。我决定删除`production`、`development`和`staging`选项，只在我的`.env`文件中指定一个数据库连接字符串。`knexfile`最后看起来像:

```
require('dotenv').config()

const pg = require('pg')
pg.defaults.ssl = true

module.exports = {
  client: 'pg',
  connection: process.env.DATABASE_URL
} 
```

Enter fullscreen mode Exit fullscreen mode

只有在 Heroku 或另一个需要 SSL 连接的提供者上使用数据库时，才需要`ssl`配置。`dotenv`允许我们从`.env`文件中检索环境变量！该变量是一个标准的 PostgreSQL 连接字符串:

```
DATABASE_URL=postgres://username:password@host:port/db_name 
```

Enter fullscreen mode Exit fullscreen mode

我在我的电脑上用`psql`创建了数据库，我用一个 [Heroku 插件](https://elements.heroku.com/addons/heroku-postgresql)创建了生产数据库。

#### 迁徙

迁移是对 ORM 中指定的数据库模式的更改，所以我们将直接用 JavaScript 定义数据库的表和列，而不是使用 SQL。

从那里，我生成了我的迁移:

```
$ knex migrate:make create_ideas
$ knex migrate:make create_comments 
```

Enter fullscreen mode Exit fullscreen mode

每个迁移命令都在`migrations`文件夹中创建了自己的单独文件。Knex 还为每个迁移添加了一个时间戳，以便迁移名称是惟一的并且按顺序运行，例如:migrations/20180218215453 _ create _ ideas . js。不过，这些可以结合起来。

迁移由
生成

```
exports.up = function (knex, Promise) {
}

exports.down = function (knex, Promise) {
} 
```

Enter fullscreen mode Exit fullscreen mode

迁移本身属于`exports.up`功能的范畴，而与此相反的迁移则属于`exports.down`范畴。`exports.down`允许我们撤销我们不再想要的移民。对于`create_ideas`的迁移，我添加了以下内容:

```
exports.up = function (knex, Promise) {
  return Promise.all([
    knex.schema.createTable('ideas', table => {
      table.increments('id').primary()
      table.string('idea')
      table.string('creator')
    })
  ])
}

exports.down = function (knex, Promise) {
  return Promise.all([
    knex.schema.dropTable('ideas')
  ])
} 
```

Enter fullscreen mode Exit fullscreen mode

根据它的文档，Knex 迁移函数应该“总是返回一个承诺”。我们可以使用`Promise.all()`来返回一组要解决的承诺。即使在这种情况下每个函数只有一个动作，我也可以添加更多由`,`分隔的动作。`exports.up`包含了`ideas`表的表创建逻辑，包括一个自动递增的主键`table.increments('id').primary()`。它还有另外两个名为`idea`和`creator`的字符串列。为了撤销迁移，我们将删除`ideas`表，如`exports.down`函数中所指定的。

创建`comments`文件的第二次迁移是类似的:

```
exports.up = function (knex, Promise) {
  return Promise.all([
    knex.schema.createTable('comments', table => {
      table.increments('id').primary()
      table.string('comment')
      table.string('creator')
      table.integer('ideas_id').references('ideas.id')
    })
  ])
}

exports.down = function (knex, Promise) {
  return Promise.all([
    knex.schema.dropTable('comments')
  ])
} 
```

Enter fullscreen mode Exit fullscreen mode

这个迁移看起来非常类似于`ideas`迁移，唯一的区别是外键:`table.integer('ideas_id').references('ideas.id')`。在文档中有许多方法可以做到这一点；然而，异议文档是这样做的，所以我也这样做了。Knex 强制使用列名`ideas_id`,而不是没有意义的`idea_id`。我确信有一种方法可以绕过命名规定；但是，我并没有花多大力气去查！

最后，我使用命令:
运行迁移

```
$ knex migrate:latest 
```

Enter fullscreen mode Exit fullscreen mode

尽管该命令暗示它只运行最新的迁移，但它会运行所有尚未运行的迁移。

#### 数据库播种

Knex 也有一些内置的功能来帮助我们播种，或者添加初始测试数据到我们的数据库中。

```
$ knex seed:make ideas 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令创建了一个包含一个`ideas.js`文件的`seeds`目录。该文件中还有以下代码:

```
exports.seed = function (knex, Promise) {
} 
```

Enter fullscreen mode Exit fullscreen mode

我添加了以下内容:

```
exports.seed = function (knex, Promise) {
  return knex('ideas').del().then(() => {
    return knex('ideas').insert([
        {creator: 'Ali', idea: 'A To Do List app!'},
        {creator: 'Ali', idea: 'A Blog!'},
        {creator: 'Ali', idea: 'A calculator'}
    ])
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这清除了`ideas`表，所以表中没有任何数据，然后它向数据库中插入三条记录。它使用 JSON 键和值来创建这些行。我只播种了`ideas`桌，但是你也绝对可以播种`comments`桌！

然后，我运行以下命令来更新数据库:

```
$ knex seed:run 
```

Enter fullscreen mode Exit fullscreen mode

#### 型号

到目前为止，我们一直使用 Knex 与我们的数据库进行交互。现在，我们将使用 Objection 创建一些模型，以便处理我们的数据库表之间的关系，并使我们的查询更加明确！我创建了一个`models`文件夹，里面有一个`schema.js`文件。无论如何，您都可以构建这个模型——一个好方法是将每个模型放在不同的文件中。不过，为了演示起见，我把所有东西都放在一起了！

首先，让我们来处理一些管理方面的事情:

```
const Knex = require('knex')
const connection = require('../knexfile')
const { Model } = require('objection')

const knexConnection = Knex(connection)

Model.knex(knexConnection) 
```

Enter fullscreen mode Exit fullscreen mode

这些代码行使用前面的`knexfile`将我们连接到数据库。我们也对我们的数据库连接提出异议。

现在，让我们为我们的`Comment`数据创建模型。这些模型将允许我们与从数据库中检索的数据进行干净的交互。

```
class Comment extends Model {
  static get tableName () {
    return 'comments'
  }

  static get relationMappings () {
    return {
      idea: {
        relation: Model.BelongsToOneRelation,
        modelClass: Idea,
        join: {
          from: 'comments.ideas_id',
          to: 'ideas.id'
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来分析一下。静态 getter 方法`tableName`返回名称`comments`:我们希望我们的`Comment`类建模的数据库表的名称！我们还有第二个静态 getter 方法，它定义了`Comment`模型与其他模型的关系。在这种情况下，外部对象`idea`的关键是我们将如何引用父类。子对象中的`relation`键具有值`Model.BelongsToOneRelation`，这表示每个评论都将有一个父想法。`modelClass`表示`idea`来自`Idea`模型，然后`join`指定数据库表和列名来执行 SQL 连接，在本例中，是从`comments`表中的`ideas_id`列到`ideas`表中的`id`列。[ES6 新增静态](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/static)和[获取](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)！

Idea 类看起来几乎完全相同，尽管关系是颠倒的！

```
class Idea extends Model {
  static get tableName () {
    return 'ideas'
  }

  static get relationMappings () {
    return {
      comments: {
        relation: Model.HasManyRelation,
        modelClass: Comment,
        join: {
          from: 'ideas.id',
          to: 'comments.ideas_id'
        }
      }
    }
  }
}

module.exports = { Idea, Comment } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们的关系是`Model.HasManyRelation`,因为一个想法可以有多个评论！我还导出了模型，以便它们可以在我们的其他文件中使用。

#### 查询

我处理的最后一个文件是`controllers/ideas.js`。我通常将我所有的“控制器”功能——决定每条路线呈现什么的路由功能——分离到一个或多个文件中，如果有很多文件的话！本周，我构建了一个 API，我将在未来为它构建一个前端。

一、一些进口:

```
const express = require('express')

const { Idea, Comment } = require('../models/schema')

const router = express.Router() 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一下第一个方法，一个 get 请求返回所有的`ideas` :

```
router.get('/', async (req, res) => {
  const ideas = await Idea.query()
  res.json(ideas)
}) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们使用`async`让 arrow 函数回调异步处理请求和响应，然后我们可以“暂停”函数体，直到来自`Idea.query()`的承诺解决。该查询将使用我们的`res.json(ideas)`方法返回一个 JavaScript 对象，其中包含我们的`ideas`表中的所有条目。如果我们导航到本地的`localhost:3000/ideas`或生产中的`https://application-ideas.herokuapp.com/ideas`，我们会看到:

```
[  {  "id":  1,  "idea":  "A To Do List app!",  "creator":  "Ali"  },  {  "id":  2,  "idea":  "A Blog!",  "creator":  "Ali"  },  {  "id":  3,  "idea":  "A calculator",  "creator":  "Ali"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

注意:异议文档使用 [async 和 await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 来处理 JavaScript 中的[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)；然而，我们可以重写上面的函数，使其看起来像下面这样，这也同样有效！

```
router.get('/', (req, res) => {
  Idea.query().then(ideas => {
    res.json(ideas)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我将把带注释的代码放在下面:

```
router.get('/:id', async (req, res) => {
  // gets one idea, found by id.
  //Also fetches the related comments using the .eager method
  const idea = await Idea.query().findById(req.params.id).eager('comments')
  res.json(idea)
})

router.post('/', async (req, res) => {
  // creates a new idea from the request body
  // only allows the idea and creator fields for safety
  const newIdea = req.body

  const idea = await Idea.query()
                         .allowInsert('[idea, creator]')
                         .insert(newIdea)

  res.send(idea)
})

router.post('/:id/comments', async (req, res) => {
  // creates a new comment that is a child of an idea, again sanitizes fields.
  const idea = await Idea.query().findById(req.params.id)

  await idea.$relatedQuery('comments')
            .allowInsert('[comment, creator]')
            .insert(req.body)

  res.send(idea)
})

router.delete('/:id', async (req, res) => {
  // deletes an idea
  await Idea.query().deleteById(req.params.id)

  res.redirect('/ideas')
})

router.delete('/:id/comments/:commentId', async (req, res) => {
  // deletes a comment
  await Comment.query().deleteById(req.params.commentId)

  res.redirect(`/ideas/${req.params.id}`)
})

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

使用异议还可以做更多的事情，比如[原始查询](http://vincit.github.io/objection.js/#raw-queries)，与 [JSON 字段的交互](http://vincit.github.io/objection.js/#json-queries)，以及[验证](http://vincit.github.io/objection.js/#validation)。

## 下一步

我和 Objection 和 Knex 一起工作真的很开心！老实说，从配置的角度来看，它非常类似于使用 Mongoose 和 MongoDB，但是它使得层次结构和相关数据更容易处理！我肯定会在未来的 Express 应用程序中继续使用这些库！如果您经常使用 Node，这绝对是一个必须尝试的方法！

下周发布:这个 API 的前端！请留下评论或给我发推文建议前端工具！

[全代码](https://github.com/aspittel/app-ideas)
[已部署 App](https://application-ideas.herokuapp.com/ideas)
[异议文档](http://vincit.github.io/objection.js)
[Knex 文档](http://knexjs.org/)

**我的一部分[关于学习新事物](https://medium.com/on-learning-new-things/learning-new-things-f4db7f16724)系列**