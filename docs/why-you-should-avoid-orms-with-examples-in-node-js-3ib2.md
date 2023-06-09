# 为什么应该避免 ORM(在 Node.js 中有例子)

> 原文：<https://dev.to/bnevilleoneill/why-you-should-avoid-orms-with-examples-in-node-js-3ib2>

[![](img/1e728a11bb1d54560a719a812aec0805.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xGTzIZeG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/756/1%2ArWu4Xve7snDgCiCbMokbSQ.png)

在这篇文章中，我们将考虑为什么你应该避免在你的项目中使用 ORM(对象关系映射)的几个原因。虽然本文中讨论的概念适用于每种语言和平台，但代码示例将使用 Node.js 风格的 JavaScript 编写，我们将考虑从 npm 存储库中获得的包。

> 首先也是最重要的:我不打算贬低这篇文章中提到的任何模块。他们每个人都付出了很多努力。它们被世界各地的生产应用程序所使用，这些应用程序每天都愉快地响应大量的请求。我也使用 ORM 部署过应用程序，没有后悔过。

### 跟着一起走

ORM 是强大的工具。本文中我们将要研究的 ORM 能够与 SQL 后端进行通信，比如 SQLite、PostgreSQL、MySQL 和 MSSQL。本文中的例子将利用 PostgreSQL，这是一个非常强大的开源 SQL 服务器。有一些 ORM 能够与 NoSQL 后端通信，比如由 MongoDB 支持的 Mongoose ORM，但是我们不会在这篇文章中考虑这些。

首先，运行以下命令在本地启动一个 PostgreSQL 实例。它的配置方式是，向 localhost:5432 上的默认 PostgreSQL 端口发出的请求将被转发到容器。它还会将文件写入主目录中的磁盘，以便后续的实例化将保留我们已经创建的数据。

```
mkdir -p ~/data/pg-node-orms
docker run 
  --name pg-node-orms 
  -p 5432:5432 
  -e POSTGRES_PASSWORD=hunter12 
  -e POSTGRES_USER=orm-user 
  -e POSTGRES_DB=orm-db 
  -v ~/data/pg-node-orms:/var/lib/postgresql/data 
  -d 
  postgres 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了一个运行的数据库，我们需要向数据库添加一些表和数据。这将允许我们对数据进行查询，并更好地理解各种抽象层。运行下一个命令启动交互式 PostgreSQL 提示符:

```
docker run 
  -it --rm 
  --link pg-node-orms:postgres 
  postgres 
  psql 
  -h postgres 
  -U orm-user 
  orm-db 
```

Enter fullscreen mode Exit fullscreen mode

在提示符下，键入上一个代码块 hunter12 中的密码。现在您已经连接好了，将下面的查询复制并粘贴到提示符下，然后按 enter 键。

```
CREATE TYPE item_type AS ENUM (
  'meat', 'veg', 'spice', 'dairy', 'oil'
);

CREATE TABLE item (
  id    SERIAL PRIMARY KEY,
  name  VARCHAR(64) NOT NULL,
  type  item_type
);

CREATE INDEX ON item (type);

INSERT INTO item VALUES
  (1, 'Chicken', 'meat'), (2, 'Garlic', 'veg'), (3, 'Ginger', 'veg'),
  (4, 'Garam Masala', 'spice'), (5, 'Turmeric', 'spice'),
  (6, 'Cumin', 'spice'), (7, 'Ground Chili', 'spice'),
  (8, 'Onion', 'veg'), (9, 'Coriander', 'spice'), (10, 'Tomato', 'veg'),
  (11, 'Cream', 'dairy'), (12, 'Paneer', 'dairy'), (13, 'Peas', 'veg'),
  (14, 'Ghee', 'oil'), (15, 'Cinnamon', 'spice');

CREATE TABLE dish (
  id     SERIAL PRIMARY KEY,
  name   VARCHAR(64) NOT NULL,
  veg    BOOLEAN NOT NULL
);

CREATE INDEX ON dish (veg);

INSERT INTO dish VALUES
  (1, 'Chicken Tikka Masala', false), (2, 'Matar Paneer', true);

CREATE TABLE ingredient (
  dish_id   INTEGER NOT NULL REFERENCES dish (id),
  item_id   INTEGER NOT NULL REFERENCES item (id),
  quantity  FLOAT DEFAULT 1,
  unit      VARCHAR(32) NOT NULL
);

INSERT INTO ingredient VALUES
  (1, 1, 1, 'whole breast'), (1, 2, 1.5, 'tbsp'), (1, 3, 1, 'tbsp'),
  (1, 4, 2, 'tsp'), (1, 5, 1, 'tsp'),
  (1, 6, 1, 'tsp'), (1, 7, 1, 'tsp'), (1, 8, 1, 'whole'),
  (1, 9, 1, 'tsp'), (1, 10, 2, 'whole'), (1, 11, 1.25, 'cup'),
  (2, 2, 3, 'cloves'), (2, 3, 0.5, 'inch piece'), (2, 13, 1, 'cup'),
  (2, 6, 0.5, 'tsp'), (2, 5, 0.25, 'tsp'), (2, 7, 0.5, 'tsp'),
  (2, 4, 0.5, 'tsp'), (2, 11, 1, 'tbsp'), (2, 14, 2, 'tbsp'),
  (2, 10, 3, 'whole'), (2, 8, 1, 'whole'), (2, 15, 0.5, 'inch stick'); 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了一个填充的数据库。您可以键入\quit 来断开与 psql 客户端的连接，并重新控制您的终端。如果您想再次运行原始 SQL 命令，您可以再次运行相同的 docker run 命令。

最后，您还需要创建一个名为 connection.json 的文件，其中包含以下 json 结构。节点应用程序稍后将使用它来连接数据库。

```
{
  "host": "localhost",
  "port": 5432,
  "database": "orm-db",
  "user": "orm-user",
  "password": "hunter12"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 层层抽象

在深入研究太多代码之前，让我们澄清几个不同的抽象层。就像计算机科学中的一切一样，当我们添加抽象层时，也有权衡。每增加一层抽象，我们都试图用开发人员生产力的提高来换取性能的下降(尽管情况并非总是如此)。

#### 低级:数据库驱动

这基本上是你能得到的最低级的东西——除了手动生成 TCP 包并将它们传递给数据库。数据库驱动程序将处理与数据库的连接(有时是连接池)。在这一级，您将编写原始 SQL 字符串，并将它们传递给数据库，并接收来自数据库的响应。在 Node.js 生态系统中，有许多库在这一层运行。这里有三个受欢迎的库:

*   mysql : MySQL (13k 星/ 330k 周下载量)
*   pg : PostgreSQL (6k 星/ 52 万周下载量)
*   sqlite3 : SQLite (3k 星/ 12 万周下载量)

这些库的工作方式基本相同:获取数据库凭证，实例化一个新的数据库实例，连接到数据库，以字符串的形式发送查询，并异步处理结果。

下面是一个简单的例子，它使用 pg 模块获取烹饪鸡肉所需的配料列表:

```
#!/usr/bin/env node 
// $ npm install pg

const { Client } = require('pg');
const connection = require('./connection.json');
const client = new Client(connection);

client.connect();

const query = `SELECT
  ingredient.*, item.name AS item_name, item.type AS item_type
FROM
  ingredient
LEFT JOIN
  item ON item.id = ingredient.item_id
WHERE
  ingredient.dish_id = $1`;

client
  .query(query, [1])
  .then(res => {
    console.log('Ingredients:');
    for (let row of res.rows) {
      console.log(`${row.item_name}: ${row.quantity}  ${row.unit}`);
    }

    client.end();
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 中间层:查询生成器

这是使用更简单的数据库驱动模块和成熟的 ORM 之间的中间层次。在这一层运行的最著名的模块是 Knex。这个模块能够为一些不同的 SQL 方言生成查询。这个模块依赖于前面提到的一个库——您需要安装计划与 Knex 一起使用的特定库。

*   [knex](https://github.com/tgriesser/knex) :查询生成器(8k 星/ 170k 周下载量)

在创建 Knex 实例时，您需要提供连接细节，以及计划使用的方言，然后就可以开始进行查询了。您编写的查询将非常类似于底层的 SQL 查询。一个好处是，您能够以编程方式生成动态查询，这比您将字符串连接在一起形成 SQL(这通常会引入安全漏洞)要方便得多。

下面是一个简单的例子，它使用 knex 模块获取烹饪鸡肉所需的配料列表:

```
#!/usr/bin/env node 
// $ npm install pg knex

const knex = require('knex');
const connection = require('./connection.json');
const client = knex({
  client: 'pg',
  connection
});

client
  .select([
    '*',
    client.ref('item.name').as('item_name'),
    client.ref('item.type').as('item_type'),
  ])
  .from('ingredient')
  .leftJoin('item', 'item.id', 'ingredient.item_id')
  .where('dish_id', '=', 1)
  .debug()
  .then(rows => {
    console.log('Ingredients:');
    for (let row of rows) {
      console.log(`${row.item_name}: ${row.quantity}  ${row.unit}`);
    }

    client.destroy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 高等级:ORM

这是我们要考虑的最高层次的抽象。当使用 ORM 时，我们通常需要提前做更多的配置。顾名思义，ORM 的目的是将关系数据库中的记录映射到应用程序中的对象(通常，但不总是，类实例)。这意味着我们在应用程序代码中定义了这些对象的结构以及它们之间的关系。

*   [序列](https://github.com/sequelize/sequelize) : (16k 星/ 270k 周下载量)
*   [书架](https://github.com/bookshelf/bookshelf):基于 Knex 星/ 23k 周下载量)
*   [水线](https://github.com/balderdashy/waterline) : (5k 星/ 20k 周下载)
*   [异议](https://github.com/Vincit/objection.js):基于 Knex 星/ 20k 周下载量)

在这个例子中，我们将看看最流行的 ORM，Sequelize。我们还将使用 Sequelize 对原始 PostgreSQL 模式中表示的关系进行建模。下面是一个使用 **Sequelize** 模块获取烹饪鸡肉所需的配料列表的例子:

```
#!/usr/bin/env node 
// $ npm install sequelize pg

const Sequelize = require('sequelize');
const connection = require('./connection.json');
const DISABLE_SEQUELIZE_DEFAULTS = {
  timestamps: false,
  freezeTableName: true,
};

const { DataTypes } = Sequelize;
const sequelize = new Sequelize({
  database: connection.database,
  username: connection.user,
  host: connection.host,
  port: connection.port,
  password: connection.password,
  dialect: 'postgres',
  operatorsAliases: false
});

const Dish = sequelize.define('dish', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  veg: { type: DataTypes.BOOLEAN }
}, DISABLE_SEQUELIZE_DEFAULTS);

const Item = sequelize.define('item', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  type: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

const Ingredient = sequelize.define('ingredient', {
  dish_id: { type: DataTypes.INTEGER, primaryKey: true },
  item_id: { type: DataTypes.INTEGER, primaryKey: true },
  quantity: { type: DataTypes.FLOAT },
  unit: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

Item.belongsToMany(Dish, {
  through: Ingredient, foreignKey: 'item_id'
});

Dish.belongsToMany(Item, {
  through: Ingredient, foreignKey: 'dish_id'
});

Dish.findOne({where: {id: 1}, include: [{model: Item}]}).then(rows => {
  console.log('Ingredients:');
  for (let row of rows.items) {
    console.log(
      `${row.dataValues.name}: ${row.ingredient.dataValues.quantity} ` +
      row.ingredient.dataValues.unit
    );
  }

  sequelize.close();
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经看到了如何使用不同的抽象层执行类似查询的例子，让我们深入了解使用 orm 时应该小心的原因。

### 原因 1:你学错了东西

很多人选择 ORM 是因为他们不想花时间去学习底层的 SQL(结构化查询语言)。人们通常认为 SQL 很难学，通过学习 ORM，我们可以简单地用一种语言而不是两种语言编写我们的应用程序。乍一看，这似乎站得住脚。ORM 将与应用程序的其他部分用相同的语言编写，而 SQL 是一种完全不同的语法。

然而，这种思路有一个问题。问题是 ORM 代表了你能得到的一些最复杂的库。ORM 的表面积非常大，从里到外了解它绝非易事。

一旦你学会了一种特定的 ORM，这种知识可能不会很好地转移。如果您从一个平台切换到另一个平台，例如从 JS/Node.js 切换到 C#/.NET，这是正确的。但是，如果您在同一个平台内从一个 orm 切换到另一个 orm，例如从 Sequelize 到 Bookshelf with Node.js，这可能就不那么明显了。考虑下面的 ORM 示例，每个示例都生成一个所有素食食谱项目的列表:

#### 排序:

```
#!/usr/bin/env node 
// $ npm install sequelize pg

const Sequelize = require('sequelize');
const { Op, DataTypes } = Sequelize;
const connection = require('./connection.json');
const DISABLE_SEQUELIZE_DEFAULTS = {
  timestamps: false,
  freezeTableName: true,
};

const sequelize = new Sequelize({
  database: connection.database,
  username: connection.user,
  host: connection.host,
  port: connection.port,
  password: connection.password,
  dialect: 'postgres',
  operatorsAliases: false
});

const Item = sequelize.define('item', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  type: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

// SELECT "id", "name", "type" FROM "item" AS "item"
//     WHERE "item"."type" = 'veg';
Item
  .findAll({where: {type: 'veg'}})
  .then(rows => {
    console.log('Veggies:');
    for (let row of rows) {
      console.log(`${row.dataValues.id}t${row.dataValues.name}`);
    }
    sequelize.close();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 书架:

```
#!/usr/bin/env node 
// $ npm install bookshelf knex pg

const connection = require('./connection.json');
const knex = require('knex')({
  client: 'pg',
  connection,
  // debug: true
});
const bookshelf = require('bookshelf')(knex);

const Item = bookshelf.Model.extend({
  tableName: 'item'
});

// select "item".* from "item" where "type" = ?
Item
  .where('type', 'veg')
  .fetchAll()
  .then(result => {
    console.log('Veggies:');
    for (let row of result.models) {
      console.log(`${row.attributes.id}t${row.attributes.name}`);
    }
    knex.destroy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 水线:

```
#!/usr/bin/env node 
// $ npm install sails-postgresql waterline

const pgAdapter = require('sails-postgresql');
const Waterline = require('waterline');
const waterline = new Waterline();
const connection = require('./connection.json');

const itemCollection = Waterline.Collection.extend({
  identity: 'item',
  datastore: 'default',
  primaryKey: 'id',
  attributes: {
    id: { type: 'number', autoMigrations: {autoIncrement: true} },
    name: { type: 'string', required: true },
    type: { type: 'string', required: true },
  }
});

waterline.registerModel(itemCollection);

const config = {
  adapters: {
    'pg': pgAdapter
  },

  datastores: {
    default: {
      adapter: 'pg',
      host: connection.host,
      port: connection.port,
      database: connection.database,
      user: connection.user,
      password: connection.password
    }
  }
};

waterline.initialize(config, (err, ontology) => {
  const Item = ontology.collections.item;
  // select "id", "name", "type" from "public"."item"
  //     where "type" = $1 limit 9007199254740991
  Item
    .find({ type: 'veg' })
    .then(rows => {
      console.log('Veggies:');
      for (let row of rows) {
        console.log(`${row.id}t${row.name}`);
      }
      Waterline.stop(waterline, () => {});
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 异议:

```
#!/usr/bin/env node 
// $ npm install knex objection pg

const connection = require('./connection.json');
const knex = require('knex')({
  client: 'pg',
  connection,
  // debug: true
});
const { Model } = require('objection');

Model.knex(knex);

class Item extends Model {
  static get tableName() {
    return 'item';
  }
}

// select "item".* from "item" where "type" = ?
Item
  .query()
  .where('type', '=', 'veg')
  .then(rows => {
    for (let row of rows) {
      console.log(`${row.id}t${row.name}`);
    }
    knex.destroy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

简单读操作的语法在这些例子中有很大的不同。随着您试图执行的操作复杂性的增加，比如涉及多个表的操作，orm 语法在不同的实现之间会有更大的不同。

光是 Node.js 的 ORM 至少有几十个，所有平台的 ORM 至少有几百个。学习所有这些工具将是一场噩梦！

幸运的是，我们只需要担心一些 SQL 方言。通过学习如何使用原始 SQL 生成查询，您可以轻松地在不同的平台之间转移这些知识。

### 原因 2:复杂的 ORM 调用可能效率低下

回想一下，ORM 的目的是获取存储在数据库中的底层数据，并将其映射到我们可以在应用程序中进行交互的对象中。当我们使用 ORM 获取某些数据时，这通常会带来一些低效。

例如，考虑我们在抽象层一节中首先看到的查询。在那个查询中，我们只是想要一个特定食谱的成分及其数量的列表。首先，我们通过手工编写 SQL 来进行查询。接下来，我们使用查询构建器 Knex 进行查询。最后，我们使用 ORM Sequelize 进行查询。让我们看一下这三个命令生成的查询:

#### 用“pg”驱动手写:

第一个查询正是我们手工编写的。它代表了获得我们想要的数据的最简洁的方法。

```
SELECT
  ingredient.*, item.name AS item_name, item.type AS item_type
FROM
  ingredient
LEFT JOIN
  item ON item.id = ingredient.item_id
WHERE
ingredient.dish_id = ?; 
```

Enter fullscreen mode Exit fullscreen mode

当我们用 EXPLAIN 作为这个查询的前缀并将其发送到 PostgreSQL 服务器时，我们得到一个成本操作 **34.12** 。

#### 用" knex "查询生成器生成:

下一个查询主要是为我们生成的，但是由于 Knex 查询构建器的显式特性，我们应该对输出结果有一个很好的预期。

```
select
  *, "item"."name" as "item_name", "item"."type" as "item_type"
from
  "ingredient"
left join
  "item" on "item"."id" = "ingredient"."item_id"
where
"dish_id" = ?; 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性，我添加了新行。除了我手写的例子中的一些小的格式和不必要的表名之外，这些查询是相同的。事实上，一旦运行解释查询，我们会得到相同的分数 **34.12** 。

#### 用“顺序化”生成 ORM:

现在让我们看看 ORM 生成的查询:

```
SELECT
  "dish"."id", "dish"."name", "dish"."veg", "items"."id" AS "items.id",
  "items"."name" AS "items.name", "items"."type" AS "items.type",
  "items->ingredient"."dish_id" AS "items.ingredient.dish_id",
  "items->ingredient"."item_id" AS "items.ingredient.item_id",
  "items->ingredient"."quantity" AS "items.ingredient.quantity",
  "items->ingredient"."unit" AS "items.ingredient.unit"
FROM
  "dish" AS "dish"
LEFT OUTER JOIN (
  "ingredient" AS "items->ingredient"
  INNER JOIN
  "item" AS "items" ON "items"."id" = "items->ingredient"."item_id"
) ON "dish"."id" = "items->ingredient"."dish_id"
WHERE
"dish"."id" = ?; 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性，我添加了新行。可以看出，这个查询与前两个查询不同。为什么它的表现如此不同？嗯，由于我们定义的关系，Sequelize 试图获得比我们要求的更多的信息。特别是，当我们真的只关心属于这道菜的配料时，我们正在获取关于这道菜本身的信息。根据解释，这个查询的成本是 **42.32** 。

### 原因三:一个 ORM 不能做所有的事情

不是所有的查询都可以用 ORM 操作来表示。当我们需要生成这些查询时，我们不得不退回到手工生成 SQL 查询。这通常意味着大量使用 ORM 的代码库仍然会有一些手写的查询。这里的含义是，作为从事这些项目之一的开发人员，我们最终需要了解 ORM 语法以及一些底层 SQL 语法。

一种不太适合 ORM 的常见情况是查询包含子查询。考虑这样一种情况，我知道我已经在数据库中购买了菜肴#2 的所有配料，但是，我仍然需要购买菜肴#1 所需的任何配料。为了得到这个列表，我可能会运行下面的查询:

```
SELECT *
FROM item
WHERE
  id NOT IN
    (SELECT item_id FROM ingredient WHERE dish_id = 2)
  AND id IN
(SELECT item_id FROM ingredient WHERE dish_id = 1); 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，这个查询不能用前面提到的 ORM 清晰地表示。为了应对这些情况，ORM 通常会提供将原始 SQL 注入查询接口的能力。

Sequelize 提供了一个. query()方法来执行原始 SQL，就像使用底层数据库驱动程序一样。通过 Bookshelf 和 Objection，您可以访问在实例化过程中提供的原始 Knex 对象，并可以使用它的查询构建器功能。Knex 对象还有一个. raw()方法来执行原始 SQL。使用 Sequelize，您还可以获得一个 Sequelize.literal()方法，该方法可用于在 Sequelize ORM 调用的各个部分散布原始 SQL。但是在每种情况下，您仍然需要了解一些底层 SQL 来生成某些查询。

### 查询构建者:最佳位置

使用低级数据库驱动模块是相当诱人的。为数据库生成查询时没有开销，因为我们是手动编写查询的。我们的项目所依赖的整体依赖性也被最小化了。然而，生成动态查询可能非常繁琐，在我看来，这是使用简单数据库驱动程序的最大缺点。

例如，考虑一个 web 界面，当用户想要检索项目时，可以在其中选择标准。如果用户只能输入一个选项，比如颜色，我们的查询可能如下所示:

```
SELECT * FROM things WHERE color = ?; 
```

Enter fullscreen mode Exit fullscreen mode

这个查询与简单的数据库驱动程序配合得很好。但是，考虑一下颜色是否是可选的，是否还有第二个可选字段 is_heavy。我们现在需要支持这个查询的几种不同排列:

```
SELECT * FROM things; -- Neither
SELECT * FROM things WHERE color = ?; -- Color only
SELECT * FROM things WHERE is_heavy = ?; -- Is Heavy only
SELECT * FROM things WHERE color = ? AND is_heavy = ?; -- Both 
```

Enter fullscreen mode Exit fullscreen mode

然而，由于上述原因，一个全功能的 ORM 也不是我们想要的工具。

在这些情况下，查询构建器是一个非常好的工具。Knex 公开的接口与底层 SQL 查询如此接近，以至于我们不得不总是知道 SQL 查询是什么样子的。这种关系类似于 TypeScript 之类的东西如何翻译成 JavaScript。

只要您完全理解它生成的底层 SQL，使用查询构建是一个很好的解决方案。永远不要把它作为一种工具来隐藏底层发生的事情。只在方便的情况下使用它，并且在你确切知道它在做什么的情况下使用它。如果您发现自己对生成的查询实际上是什么样子有疑问，您可以向 Knex()实例化调用添加一个调试字段。这样做看起来像这样:

```
const knex = require('knex')({
  client: 'pg',
  connection,
  debug: true // Enable Query Debugging
}); 
```

Enter fullscreen mode Exit fullscreen mode

事实上，本帖中提到的大多数库都包含了某种调试正在执行的调用的方法。

我们已经研究了抽象数据库交互的三个不同层次，即低级数据库驱动程序、查询构建器和高级 ORM。我们还研究了使用每一层以及生成的 SQL 查询的利弊:这包括使用数据库驱动程序生成动态查询的困难、ORM 增加的复杂性，以及使用查询生成器的好处。

感谢您的阅读，并确保在您构建下一个项目时考虑到这一点。

一旦你完成了下面的步骤，你可以运行下面的命令来完全删除 docker 容器，并从你的计算机中删除数据库文件:

```
docker stop pg-node-orms
docker rm pg-node-orms
sudo rm -rf ~/data/pg-node-orms 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[为什么你应该避免 ORMs(在 Node.js 中有例子)](https://blog.logrocket.com/why-you-should-avoid-orms-with-examples-in-node-js-e0baab73fa5/)首先出现在[博客](https://blog.logrocket.com)上。