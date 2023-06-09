# 业余爱好者的冒险~第四部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-four-2i9k>

# 玩弄`MySQL`和`HTTP`

[![database](img/4bd619212900de765ba69fcaf5613b4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VoNoN7mD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/en/7/78/DB-database-icon.png)

## 这是什么？

嗨，你正在阅读这个系列的第四部分，如果你有兴趣阅读我到目前为止写的东西，你可以在这里找到帖子:

*   [序言](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-one-2e0n)
*   [学习第一周](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-two-2g5a)
*   [思考`conf`文件](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-three-l1m)

也就是说，如果你刚刚开始，这里有一个非常快速的跑步路线；我在 IT 支持部门工作，这项工作需要直接与终端交互，但人们会犯错误，所以我编写了一些软件来通过 Samba 创建用户。不幸的是，该软件感觉有点笨拙，尽管它在办公室很受欢迎，并希望重新编写它。我决定将这个项目开源，记录我的旅程。

## [MySQL](#mysql)

我还没有决定使用哪种数据库软件(GitHub 上有一个公开的[问题](https://github.com/ignis-pwa/ideas/issues/4)，如果你想发表意见的话)，有人向我建议过`MongoDB`，但是因为我习惯了 MySQL，所以我也在研究这个问题。我认为在这个过程中学习新的东西对我来说很重要，即使我最终不会在最终项目中使用它们。

### 代码

和大多数东西节点一样，我找到了一个我可以使用的[模块](https://www.npmjs.com/package/mysql),并着手编写一个非常简单的测试程序。

```
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: 'hostname',
  user: 'hostuser',
  password: 'password',
  database: 'database'
});

connection.connect();

let table = "user_details";

connection.query(`SELECT * FROM ${table}`, (err, res) => {
  if (err) throw err;
  for (let result of res) {
    console.log(`${JSON.stringify(result)}`)
  }
});

connection.end(); 
```

Enter fullscreen mode Exit fullscreen mode

我说过，很简单。它只是为表中的每个用户向控制台输出一行数据。

## HTTP

从长远来看，我想使用 HTTPS 或者甚至 HTTP/2，但是我需要先把基础的东西做好，我有一个习惯，在一个项目中跑在前面，陷入困境/困惑，然后失去兴趣。稳扎稳打才能赢得比赛。

### 代码

我相信你们中的许多人，如果不是所有人，都见过一个简单的 HTTP 例子，但为了以防万一，我还是在这里举了一个例子。

```
const http = require('http');

http.createServer((req, res) => {
  res.write('Hello World!');
  res.end();
}).listen(80); 
```

Enter fullscreen mode Exit fullscreen mode

现在只需浏览到`localhost/`，我们就有一个白色的页面，顶部写着`Hello World!`。另一个非常简单、无聊的例子。

## 拼凑

由于这两个例子都很简单，我决定离开赛道，将两者混合在一起，我还没有完全实现路由，所以我的代码在服务器脚本中，我想这是非常糟糕的做法。

我决定编写一个简单的脚本，它可以在 MySQL 表中查找并将结果输出到 HTML 表中。我的主要想法是，它很简单，可以简单地组合在一起，到最后我会有所发现。

### 代码

```
const http = require('http');
const mysql = require('mysql');

const database = mysql.createConnection({
  host: 'hostname',
  user: 'hostuser',
  password: 'password',
  database: 'database'
});

let table = "user_details";

// Variable for storing HTML
let outputTable = "";

// Database connection
database.connect();
database.query(`SELECT * FROM ${table}`, (err, res) => {
  if (err) throw err;
  // Push results to `outputTable`
  outputTable += "<div class='table-container'><table>"
  for (let key of Object.keys(res[0])) {
    outputTable += `<th>${key}</th>`
  }
  for (let result of res) {
    outputTable += `<tr>`
    for (let key of Object.keys(result)) {
      outputTable += `<td>${result[key]}</td>`
    }
    outputTable += `</tr>`
  }
  outputTable += "</table>"
});

database.end();

// CSS
let styles = `<style>
  .table-container {
    max-width: 85%;
    width: 1024px;
    border-radius: 4px;
    overflow: hidden;
    margin: 0 auto;
    box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
  }
  table {
    border-collapse: collapse;
    width: 100%;
  }
  th {
    font-size: 18px;
    color: #fff;
    line-height: 1.4;
    padding: 2px 5px;
    background-color: #6c7ae0
  }
  td {
    font-size: 15px;
    color: #808080;
    line-height: 1.4;
    padding: 0px 5px;
  }
  tr:nth-child(even) {
    background-color: #f8f6ff;
  }
</style>`

http.createServer((req, res) => {
  res.write(styles);
  res.write(outputTable);
  res.end();
}).listen(80); 
```

Enter fullscreen mode Exit fullscreen mode

我肯定你们中的许多人对我的代码感到畏缩，但是，正如我所说的，这只是一种把我学到的东西放在一起的方法。随着时间的推移，我将学会正确的做事方法。

如果你真的想让我明白，你可以在这里留下评论，我会阅读它，并从现在开始尝试将它融入我的编码实践中，你的任何贡献都会非常有帮助。

### 输出

以防你好奇，这是表格:

[![HTML Table](img/8d8e4778105791054f13b03569855dd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GcMAXgf2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilyisgio31hrd108jmak.png)

## 又一个搞定了。

哇，这是一个漫长的。感谢您的阅读。我想知道这些帖子的标题是否需要更改`Adventures of a Hobbyist ~ Part ${n}`没问题，但感觉可能有点乏味，你们觉得呢？和往常一样，如果我做的事情有什么明显的错误，或者你有什么要补充的，请随时发表评论或者到我的 [GitHub](https://github.com/ignis-pwa) 留言。

下周我就要休假了，但是实际上这周我已经做了一些我可以写的东西，主要是关于从上一篇文章延续下来的更多的配置内容。我可能会写一篇文章，然后在下周发表。如果没有，我将不会发布，直到我回来(08/28)。

再次感谢你阅读❤️