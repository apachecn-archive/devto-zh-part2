# 用 PostgreSQL 持久化一个节点 API，不需要 ORM 的帮助。

> 原文：<https://dev.to/ogwurujohnson/-persisting-a-node-api-with-postgresql-without-the-help-of-orms-like-sequelize-5dc5>

当我们使用术语“持续”时，我们的意思是什么？在我们的日常英语中，持久性是“一个结果在它的原因被移除后的持续性”，在将数据存储在数据库中的上下文中，持久性指的是记录在创建它的过程结束后仍然存在的能力。

不像其他 NodeJS 教程那样使用 MongoDB 进行数据持久化，我们将使用 PostgreSQL。另外，为了确保我们掌握使用 NodeJs 和 PostgreSQL 开发 Restful API 的基础知识，我们将避免使用 ORM(对象关系映射),请阅读更多相关内容👉[此处](https://en.wikipedia.org/wiki/Object-relational_mapping)。

**先决条件**:

1.  浏览我关于用 Express 和 Es6 编写基本 NodeJs API 的课程，你可以在下面找到它👇[![ogwurujohnson](img/5a9b1758658be88756f92622cacdc96a.png)](/ogwurujohnson) [## 带有 NodeJs 和 ES6 的简单 Restful Api .

    ### 约翰逊 奥格武鲁 9 月 28 日 186 分钟阅读

    #节点# JavaScript#教程](/ogwurujohnson/basic-api-with-nodejs-1el8)
2.  对 SQL 查询语法有一个基本的了解，因为本教程不会涉及这些
3.  NodeJs 和 Express 的基本知识
4.  已经安装了 NodeJs，下面是下载[链接](https://nodejs.org/en/download/)
5.  已经安装了 PostgreSQL，下面是下载[链接](https://www.postgresql.org/download/)
6.  安装后，搜索应用程序`pgAdmin`并启动它，它随 PostgreSQL 一起安装。pgAdmin 是 PostgreSQL 的 GUI 管理和开发平台(这意味着它有助于使我们的数据库管理更加容易和友好)

**入门:**
在本教程中，我们将为一个类注册表创建一个 API，我们将开始创建和设置我们的数据库。

按照下面的说明使用 pgAdmin:
`1\. Double click on the PostgreSQL server`创建数据库。

`2\. Right-lick on Login Roles, assuming you have none and select new login role`。

`3\. Type your preferred Role name, mine is school_reg`。

`4\. Move to the next tab *Definition* and set your password for this role, mine is school_reg`。

`5\. Click on *OK* to save`。

`6\. Right-click on Databases and select New Database`。

`7\. Type Database name in the space for Name, mine is school_register`。

`8\. Select owner of Database, here we would select the login role or user we created, remember mine is school_reg`。

`9\. Click *OK* to save`。

上图说明了上面的过程，放大看清楚:

[![Image illustrating creation of Database](img/8281bc0ba9cf1de76e93af8d2414973d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eAToWpk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12ohrcjxdpiubxl2rjn3.png)

创建数据库后，我们需要创建表和表关系，用极客的话来说，*让我们创建我们的模式*。现在我们只有一个表，我们的`student`表，它包含学生的基本信息，随着应用程序的增长，如果我们选择扩展，我们将不得不创建更多的表。

**项目设置:**

1.  我们将从创建我们的项目文件夹开始，我将我的文件夹命名为`AwesomeNodepostgres`，是的，我知道它很长，你可以自由选择一个较短的名称。

2.  当您从命令行找到项目文件夹时，通过从命令行运行`npm init`将您的项目初始化为节点项目。如果你还没有这样做，请参考上一篇教程来弄清楚如何做。

经过以上步骤，你的项目目录应该是这样的，假设你使用的是我最喜欢的代码编辑器 [vscode。](https://code.visualstudio.com/download)
[![vscode shot](img/7ae09862ab56f2554f40db5d7432ef1e.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--qPSHBbY3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ztjxqe2gbkh10dbjrav9.PNG)

现在这已经解决了，让我们从安装一些编写应用程序所需的包开始。在本教程中，我们将需要以下节点包；

1.  Express，这是我们在上一篇文章中已经遇到的
2.  为了与我们的 postgres 数据库通信，我们需要一个名为 [node-postgres](https://node-postgres.com/) 的包。
3.  为了确保我们的服务器总是监听变化并重启我们的应用程序，我们还将安装另一个包 [nodemon](https://www.npmjs.com/package/nodemon) 。

要安装前两个包，请确保从命令行进入项目文件夹。然后运行以下命令；

```
 npm install express pg --save
            npm install nodemon --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

`--save-dev`这里的意思是保存，只在开发期间使用 nodemon 作为开发依赖，所以在生产期间不会使用 nodemon。

现在，为了建立数据库连接，我们将创建一个文件夹`services`，并在该文件夹中创建一个名为`db.js`的文件。
所以在`services/db.js`里面，键入下面的代码；

```
const pg = require('pg');

const config = {
  user: 'school_reg', //this is the db user credential
  database: 'school_register',
  password: 'school_reg',
  port: 5432,
  max: 10, // max number of clients in the pool
  idleTimeoutMillis: 30000,
};

const pool = new pg.Pool(config);

pool.on('connect', () => {
  console.log('connected to the Database');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在要创建我们的表，在前面的代码块下键入下面的代码；

```
const createTables = () => {
  const schoolTable = `CREATE TABLE IF NOT EXISTS
      students(
        id SERIAL PRIMARY KEY,
        student_name VARCHAR(128) NOT NULL,
        student_age INT NOT NULL,
        student_class VARCHAR(128) NOT NULL,
        parent_contact VARCHAR(128) NOT NULL,
        admission_date VARCHAR(128) NOT NULL
      )`;
  pool.query(schoolTable)
    .then((res) => {
      console.log(res);
      pool.end();
    })
    .catch((err) => {
      console.log(err);
      pool.end();
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了用这个脚本创建我们的表，我们需要找到一种方法在命令行上运行这个代码。我们可以使用另一个名为`make-runnable`的包，安装这个包；`npm install make-runnable --save`，安装完成后，回到你的代码库页面下方，添加以下内容；

```
pool.on('remove', () => {
  console.log('client removed');
  process.exit(0);
});

//export pool and createTables to be accessible  from an where within the application
module.exports = {
  createTables,
  pool,
};

require('make-runnable'); 
```

Enter fullscreen mode Exit fullscreen mode

现在创建我们的应用程序，让我们在我们的`package.json`文件上写一个脚本，当从命令行运行时，它将为我们创建一个表。在您的`package.json`文件中找到对象脚本，用下面的代码替换对象内部的内容；

```
"create": "node ./services/db createTables" 
```

Enter fullscreen mode Exit fullscreen mode

仍然在项目目录中时，转到命令行并运行；

```
npm run create 
```

Enter fullscreen mode Exit fullscreen mode

下图显示了当您这样做时会发生什么，并且仔细查看了您将在 pgAdmin 应用程序上定位新创建的表的位置。

[![runing npm run create](img/2def3dde1302c9775c57caedd5994279.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oM2MS0IJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/13p79ijvxdpe1485kw9k.PNG)

[![viewing newly created table](img/51231c8ba0b4e8dae64bb7da2964cd19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VIAaKY2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fbd3ceify55uu6ws76ju.PNG)

随着数据库和表的创建完成，是时候开始在数据库中存储数据并检索这些存储的数据了。

在此之前，让我们创建我们的`index.js`文件，它将包含我们的服务器和 express 代码，在项目文件夹中创建一个名为`index.js`的文件。在我们的`index.js`中输入下面的代码。

```
 const express = require('express');
   const app = express();

   const port = process.env.PORT || 3000;

   // Add route code Here
   app.get('/', (req, res) => {
      res.send('Welcome to Our SCHOOL API');
   });

   app.listen(port, () => {
      console.log(`We are live at 127.0.0.1:${port}`);
   }); 
```

Enter fullscreen mode Exit fullscreen mode

要运行我们的应用程序，让我们在`package.json`文件中找到的`scripts`对象上添加两个额外的代码，在创建之前，删除这两个代码；

```
 "dev": "nodemon index.js",
    "start": "node index.js", 
```

Enter fullscreen mode Exit fullscreen mode

[![scripts for running](img/e4bc4ff94bca38eaf0a0e0962eb6faad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tppLIN1s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xzmkpbst4blgcvk2sbed.PNG)

现在要运行我们的项目，我们将使用 nodemon 选项，所以无论我们做什么更改，我们都能够注意到。当您做出更改并保存它时，尝试观察 nodemon 如何重新启动服务器。

[![nodemon starting server](img/f7f9553224fa5dc89b25755d923772f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f9yEkfYS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/be0e50wwq98koh8pmw5g.PNG)

当我们的服务器启动时，访问你的服务器正在监听的链接，`127.0.0.1:3000`正如我的服务器所指定的。打开您的 postman 应用程序，并使用该链接发出 GET 请求。

[![postman](img/d8ba438addea34dc40b3c05a962c627d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U4EhxoDq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fegqytuigbeclqeezbu3.PNG)

现在，让我们创建处理创建学生和获取所有学生的路由。在你的`index.js`文件的第一个路径之后，添加下面的代码，这些代码将处理，插入一个学生，获取所有学生。为了能够插入到我们的数据库中，我们需要能够选择提供给任何请求体的值，为此我们将使用一个名为 [body-parser](https://www.npmjs.com/package/body-parser) 的节点包。

```
npm install body-parser 
```

Enter fullscreen mode Exit fullscreen mode

在安装了上面的包之后，在继续我们的路线之前，将下面的代码添加到我们的`index.js`中。在下面添加以下内容`const app = express();`

```
const bodyParser = require('body-parser');
app.use(bodyParser.json());
app.use(bodyparser.urlencoded({extended:true})); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们继续我们的路线；

```
app.get('/student', (req, res) => {
pool.connect((err, client, done) => {
    const query = 'SELECT * FROM students';
    client.query(query, (error, result) => {
      done();
      if (error) {
        res.status(400).json({error})
      } 
      if(result.rows < '1') {
        res.status(404).send({
        status: 'Failed',
        message: 'No student information found',
        });
      } else {
        res.status(200).send({
        status: 'Successful',
        message: 'Students Information retrieved',
        students: result.rows,
        });
      }
    });
  });
});

app.post('/student', (req, res) => {
  const data = {
    name : req.body.studentName,
    age : req.body.studentAge,
    classroom : req.body.studentClass,
    parents : req.body.parentContact,
    admission : req.body.admissionDate,
  }

  pool.connect((err, client, done) => {
    const query = 'INSERT INTO students(student_name,student_age, student_class, parent_contact, admission_date) VALUES($1,$2,$3,$4,$5) RETURNING *';
    const values = [data.name, data.age, data.classroom, data.parents, data.admission];

    client.query(query, values, (error, result) => {
      done();
      if (error) {
        res.status(400).json({error});
      }
      res.status(202).send({
        status: 'SUccessful',
        result: result.rows[0],
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行您的应用程序，访问 postman 上的链接，这一次访问每个请求的 GET 和 POST 之间交替的链接`127.0.0.1/student`。看看下面的截图，如果你不知道如何用 postman 测试，做完全相同的事情。

1.  首先你在邮递员上设置你的头信息:
    [![header information](img/fa45ffc7dd6ced3c54077b01956a74af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Xew142N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3wzwl1ry2li1651o03y.PNG)

2.  之后，我们将设置体值，如下图:
    [![body config](img/19984d3191c818450baf7098beb3925d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uejs0-Cl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b20ynnvjsvx80os60xbr.PNG)

3.  然后你点击发送，你就会得到你的结果
    [![result](img/d5251ba007dd10a99bfaa735cbb2ce5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FPwD3q3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/91r3z8iyu5ogorsi6p0s.PNG)

现在要运行 get 请求，请将请求类型更改为 GET，然后单击 send。

[![get](img/1d1b5e60a99c08bd2f4f5c864a16ab44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tu_NMjpb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/76hvmzqtj5t75b8mup1g.PNG)

沃拉。！！现在我们有了 API 获取并发送到数据库。

**注意**:将我们的 route、server 和 express 应用程序放在一个文件中是不好的做法，在 GitHub 上启动这个项目库，fork 并提交 PR，当我继续工作时，学习如何做，或者你可以尝试自己工作并提交一个 push 请求。

让我们在我们的应用程序中再添加一个路由，假设我们想要获取一个特定的学生信息，我们将编写路由，但是我希望您编写查询并根据您所学的内容返回一个响应。

```
app.get('/student/:id', (req,res) => {
  const id = req.params.id;
  res.send(`Student ${id} profile`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当我们向新的端点发出请求时，我们会得到:

[![individual student](img/674aaabffeaf4b6c354d8c67aea59823.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8K57dPsy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ueg6oyhidf35e7ac9eq.PNG)

因此，尝试完成代码，从数据库中选择您所引用的 id 的学生。

下面附上承诺的 GitHub 回购。，别忘了明星。谢了。

在我们的下一个教程中，我们将会看到如何认证我们的路由，所以没有正确的认证，用户就不能访问路由。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [奥格武鲁强森](https://github.com/ogwurujohnson) / [ AwesomeNodePostgres](https://github.com/ogwurujohnson/AwesomeNodePostgres)

### 这是一个项目，旨在教授在使用 NodeJs 创建 API 时如何使用 PostgreSQL 持久化数据

<article class="markdown-body entry-content container-lg" itemprop="text">**N**ode **E**xpress **P**postgres 

# **开发到**的节点 API 教程的代码库

### 在数据库中存储记录

### 从数据库中检索记录

### 更新数据库中的记录等

[![image](img/12c35acf65122b97bae0c662e75509b2.png)T2】](https://user-images.githubusercontent.com/14821816/47576850-65dcc600-d93d-11e8-9b70-93754a902a84.png)

## 项目

[![GitHub issues](img/411d3d7903c608404c2d1257a7b595b3.png)](https://github.com/ogwurujohnson/AwesomeNodePostgres/issues)[![GitHub forks](img/790771e798029e10c9621295b26e3303.png)](https://github.com/ogwurujohnson/AwesomeNodePostgres/network)[![GitHub stars](img/d83dbf46c46f1558dad1707c2fc28cbe.png)](https://github.com/ogwurujohnson/AwesomeNodePostgres/stargazers)[![GitHub license](img/2e3f640db9a9d9e366819c8f56ba9859.png)](https://github.com/ogwurujohnson/AwesomeNodePostgres/blob/master/LICENSE)[![price](img/544ae72165fec19dbdaf9af41f363c8b.png)](https://camo.githubusercontent.com/c26c1d49c6f2f99bd3c2768edc5bb0f067794f81cf88119b8b44d734c70b6c72/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50726963652d467265652d677265656e2e737667)

[![Twitter](img/08552cbcee9a9d4c457926a1b96a1678.png)T2】](https://twitter.com/intent/tweet?text=Wow:&url=https%3A%2F%2Fgithub.com%2Fogwurujohnson%2FAwesomeNodePostgres)

# awesomenodepositgres

这是一个项目，旨在教授如何在使用 NodeJs 创建 API 的同时使用 PostgreSQL 持久化数据。我们将致力于创建一个课堂应用程序，一个用于处理查看所有学生、添加学生、编辑学生详细信息、删除学生等的 API。您也可以随意添加其他功能，为这个项目做贡献，并在 README.md 文件中留下如何使用它的指导

</article>

[View on GitHub](https://github.com/ogwurujohnson/AwesomeNodePostgres)