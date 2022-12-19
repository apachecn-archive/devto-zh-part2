# 使用 node js 和 mysql 构建一个简单的应用程序

> 原文：<https://dev.to/achowba/build-a-simple-app-using-node-js-and-mysql-19me>

你好世界！。在本教程中，我们将使用 Node JS 和 MySQL 构建一个简单的 CRUD 应用程序。

## 我们将建造什么？

[![](../Images/775065de42740145ac08974f53a239c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---uE-ivP0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1529694163/node-mysql-crud-app/homepage2.png) 
上图为 app。这是一个应用程序，让您添加球员到一个数据库中，并显示他们的详细资料从数据库中。您还可以删除和编辑玩家的详细信息。

## 先决条件

在您加入本教程之前，我们假设您符合下列要求:

*   安装在您的 PC 上的节点 JS。
*   基本了解 Node JS 和 Express JS。
*   SQL 知识，你应该知道和了解如何查询一个数据库。
*   phpmyadmin 安装在您的电脑上。我建议安装 xampp，因为它已经包含了 phpmyadmin。
*   理解如何使用模板引擎——我们将在本教程中使用 ejs。
*   您选择的文本编辑器或 IDE。

## 文件夹结构

这就是项目的结构。

```
├── node-mqsql-crud-app (main directory)   
    ├── node_modules
    ├── public
        ├── assets 
            ├── img
    ├── routes
        ├── index.js
        ├── player.js
    ├── views
        ├── partials 
            ├── header.ejs
        ├── index.ejs
        ├── add-player.ejs
        ├── edit-player.ejs
    ├── app.js 
```

Enter fullscreen mode Exit fullscreen mode

## 为项目创建目录

在合适的目录中打开命令提示符，并键入以下命令:

```
mkdir node-mysql-crud-app 
```

Enter fullscreen mode Exit fullscreen mode

然后通过键入以下命令
切换到该目录

```
cd node-mysql-crud-app 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化项目

在项目目录中打开命令提示符，并在下面键入命令:

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

## 安装需要的模块。

成功构建应用程序需要以下模块。

*   **[express](https://www.npmjs.com/package/express)** :用于创建句柄路由和处理来自客户端的请求。
*   **[express-file upload](https://www.npmjs.com/package/express-fileupload)**:简单的包裹在卫生员周围的 express 文件上传中间件。
*   :用于解析客户端传入的请求。
*   **[MySQL](https://www.npmjs.com/package/mysql)**:MySQL 的节点 JS 驱动。
*   **[ejs](https://www.npmjs.com/package/ejs)** :为 app 渲染 html 页面的模板引擎。
*   **[req-flash](https://www.npmjs.com/package/req-flash)** :用于向视图发送 flash 消息
*   **[nodemon](https://www.npmjs.com/package/nodemon)** :全球安装。它用于监视文件的更改并自动重启服务器。

键入以下命令，将前 7 个模块作为依赖项安装。

```
npm install express express-fileupload body-parser mysql ejs req-flash --save 
```

Enter fullscreen mode Exit fullscreen mode

然后键入以下命令，在您的 PC 上全局安装最后一个模块。

```
npm install nodemon -g 
```

Enter fullscreen mode Exit fullscreen mode

## 为 app 创建数据库

复制下面的命令，导航到 phpmyadmin 仪表板，在控制台(通常位于页面底部)中执行下面的查询，以便为应用程序创建数据库和表。

```
CREATE DATABASE socka;
CREATE TABLE IF NOT EXISTS `players` (
  `id` int(5) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(255) NOT NULL,
  `last_name` varchar(255) NOT NULL,
  `position` varchar(255) NOT NULL,
  `number` int(11) NOT NULL,
  `image` varchar(255) NOT NULL,
  `user_name` varchar(20) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=1; 
```

Enter fullscreen mode Exit fullscreen mode

## 添加视图

### [弹头](#headerejs)

**header.ejs** 文件将位于 ***/views/partials*** 文件夹中，它将包含在项目的其余部分中。

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <!--<link rel="stylesheet" href="/assets/css/custom.css">-->
    <%= title %>
</head>
<style>
    .table-wrapper {
        margin-top: 50px;
    }

    .player-img {
        width: 40px;
        height: 40px;
    }

    .add-player-form {
        margin-top: 50px;
    }
</style>
<body>
<div class="page-wrapper">
    <nav class="navbar navbar-light bg-light">
        <span class="navbar-brand mb-0 h1" ><a href="/">Socka Players</a></span>
        <a class="float-right" href="/add" title="Add a New Player">Add a Player</a>
    </nav> 
```

Enter fullscreen mode Exit fullscreen mode

### [指数. ejs](#indexejs)

> 这是该应用程序的主页，其中包含一个显示所有球员名单的表格。

```
<% include partials/header.ejs %>
    <div class="table-wrapper">
        <% if (players.length > 0) {%>
            <table class="table table-hovered">
                <thead class="thead-dark">
                    <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Image</th>
                        <th scope="col">First Name</th>
                        <th scope="col">Last Name</th>
                        <th scope="col">Position</th>
                        <th scope="col">Number</th>
                        <th scope="col">Username</th>
                        <th scope="col">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <% players.forEach((player, index) => { %>
                        <tr>
                            <th scope="row"><%= player.id %></th>
                            <td><img src="/assets/img/<%= player.image %>" class="rounded-circle player-img" alt=""></td>
                            <td><%= player.first_name %></td>
                            <td><%= player.last_name %></td>
                            <td><%= player.position %></td>
                            <td><%= player.number %></td>
                            <td>@<%= player.user_name %></td>
                            <td>
                                <a href="/edit/<%= player.id %>" target="_blank" rel="noopener" class="btn btn-sm btn-success">Edit</a>
                                <a href="/delete/<%= player.id %>" class="btn btn-sm btn-danger">Delete</a>
                            </td>
                        </tr>
                    <% }) %>
                </tbody>
            </table>
        <% } else { %>
            <p class="text-center">No players found. Go <a href="/add" >here</a> to add players.</p>
        <% } %>
    </div>
</div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### add-player.ejs

> 此页面包含向数据库添加新玩家的表单。

```
<% include partials/header.ejs %>
    <div class="container">
        <% if (message != '') { %>
            <p class="text-center text-danger"><%= message %></p>
        <% } %>
        <form class="add-player-form" action="" method="post" enctype="multipart/form-data">
            <div class="form-row">
                <div class="form-group col-md-4">
                    <input type="text" class="form-control" name="first_name" id="first-name" placeholder="First Name" required>
                </div>
                <div class="form-group col-md-4">
                    <input type="text" class="form-control" name="last_name" id="last-name" placeholder="Last Name" required>
                </div>
                <div class="form-group col-md-4">
                    <input type="text" class="form-control" name="username" id="username" placeholder="Username" required>
                </div>
            </div>
            <div class="form-row">
                <div class="form-group col-md-6">
                    <input type="number" class="form-control" name="number" id="number" placeholder="Number" required>
                </div>
                <div class="form-group col-md-6">
                    <select id="position" name="position" class="form-control" required>
                        <option selected disabled>Choose position</option>
                        <option>Goalkeeper</option>
                        <option>Defender</option>
                        <option>Midfielder</option>
                        <option>Forward</option>
                    </select>
                </div>
                <div class="col-md-12">
                    <label for="player-img"><b>Player Image</b></label><br>
                    <input type="file" name="image" id="player-img" class="" required>
                </div>
            </div>
            <button type="submit" class="btn btn-primary float-right">Add Player</button>
        </form>
    </div>
</div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 编辑-player.ejs

> 此页面包含编辑添加到数据库中的球员的表单。

```
<% include partials/header.ejs %>
<div class="container">
    <% if (message) { %>
        <p class="text-center text-danger"><%= message %></p>
    <% } %>

    <% if (player) { %>
        <form class="add-player-form" action="" method="post" enctype="multipart/form-data">
            <div class="form-row">
                <div class="form-group col-md-4">
                    <label for="first-name">First Name</label>
                    <input type="text" class="form-control" name="first_name" id="first-name" value="<%= player.first_name %>" required>
                </div>
                <div class="form-group col-md-4">
                    <label for="last-name">Last Name</label>
                    <input type="text" class="form-control" name="last_name" id="last-name" value="<%= player.last_name %>" required>
                </div>
                <div class="form-group col-md-4">
                    <label for="username">Username</label>
                    <input type="text" class="form-control" name="username" id="username" value="<%= player.user_name %>" required disabled title="Username cannot be edited.">
                </div>
            </div>
            <div class="form-row">
                <div class="form-group col-md-6">
                    <label for="number">Number</label>
                    <input type="number" class="form-control" name="number" id="number" placeholder="Number" value="<%= player.number %>" required>
                </div>
                <div class="form-group col-md-6">
                    <label for="position">Position</label>
                    <select id="position" name="position" class="form-control" required>
                        <option selected><%= player.position %></option>
                        <option>Goalkeeper</option>
                        <option>Centre Back</option>
                        <option>Right Back</option>
                        <option>Left Back</option>
                        <option>Defensive Midfielder</option>
                        <option>Central Midfielder</option>
                        <option>Attacking Midfielder</option>
                        <option>Right Wing Forward</option>
                        <option>Left Wing Forward</option>
                        <option>Striker</option>
                    </select>
                </div>
            </div>
            <button type="submit" class="btn btn-success float-right">Update Player</button>
        </form>
    <% } else { %>
        <p class="text-center">Player Not Found. Go <a href="/add">here</a> to add players.</p>
    <% } %>
</div>
</div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 在服务器上工作。

### App.js

```
const express = require('express');
const fileUpload = require('express-fileupload');
const bodyParser = require('body-parser');
const mysql = require('mysql');
const path = require('path');
const app = express();

// const {getHomePage} = require('./routes/index');
// const {addPlayerPage, addPlayer, deletePlayer, editPlayer, editPlayerPage} = require('./routes/player');
const port = 5000;

// create connection to database
// the mysql.createConnection function takes in a configuration object which contains host, user, password and the database name.
const db = mysql.createConnection ({
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'socka'
});

// connect to database
db.connect((err) => {
    if (err) {
        throw err;
    }
    console.log('Connected to database');
});
global.db = db;

// configure middleware
app.set('port', process.env.port || port); // set express to use this port
app.set('views', __dirname + '/views'); // set express to look in this folder to render our view
app.set('view engine', 'ejs'); // configure template engine
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json()); // parse form data client
app.use(express.static(path.join(__dirname, 'public'))); // configure express to use public folder
app.use(fileUpload()); // configure fileupload

// routes for the app
/*
app.get('/', getHomePage);
app.get('/add', addPlayerPage);
app.get('/edit/:id', editPlayerPage);
app.get('/delete/:id', deletePlayer);
app.post('/add', addPlayer);
app.post('/edit/:id', editPlayer);
*/

// set the app to listen on the port
app.listen(port, () => {
    console.log(`Server running on port: ${port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 在上面的代码中，模块是必需的，然后创建一个到数据库的连接。mysql.createConnection 函数接受一个对象，该对象包含所连接的数据库的配置。在下一条语句中，数据库被连接。在命令提示符下运行下面的代码来运行服务器。

```
nodemon app.js 
```

Enter fullscreen mode Exit fullscreen mode

你的控制台应该显示如下结果:
[![](../Images/e502d2d92e435c5f45116f7afef87871.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S2PBZyV5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1529611843/node-mysql-crud-app/app-js-1.png)

## 添加路线

### index.js

将下面的代码复制到 ***/routes*** 目录下的 index.js 文件中。

```
module.exports = {
    getHomePage: (req, res) => {
        let query = "SELECT * FROM `players` ORDER BY id ASC"; // query database to get all the players

        // execute query
        db.query(query, (err, result) => {
            if (err) {
                res.redirect('/');
            }
            res.render('index.ejs', {
                title: Welcome to Socka | View Players
                ,players: result
            });
        });
    },
}; 
```

Enter fullscreen mode Exit fullscreen mode

> **db.query** 函数查询数据库。它接受查询和字符串以及一个接受两个参数的回调函数，如果查询成功，结果被传递给视图中的 ***res.render*** 函数。

### player.js

**player.js** 文件将包含球员页面的所有路径，例如添加球员、更新球员的详细信息和删除球员。

```
const fs = require('fs');

module.exports = {
    addPlayerPage: (req, res) => {
        res.render('add-player.ejs', {
            title: Welcome to Socka | Add a new player
            ,message: ''
        });
    },
    addPlayer: (req, res) => {
        if (!req.files) {
            return res.status(400).send("No files were uploaded.");
        }

        let message = '';
        let first_name = req.body.first_name;
        let last_name = req.body.last_name;
        let position = req.body.position;
        let number = req.body.number;
        let username = req.body.username;
        let uploadedFile = req.files.image;
        let image_name = uploadedFile.name;
        let fileExtension = uploadedFile.mimetype.split('/')[1];
        image_name = username + '.' + fileExtension;

        let usernameQuery = "SELECT * FROM `players` WHERE user_name = '" + username + "'";

        db.query(usernameQuery, (err, result) => {
            if (err) {
                return res.status(500).send(err);
            }
            if (result.length > 0) {
                message = 'Username already exists';
                res.render('add-player.ejs', {
                    message,
                    title: Welcome to Socka | Add a new player
                });
            } else {
                // check the filetype before uploading it
                if (uploadedFile.mimetype === 'image/png' || uploadedFile.mimetype === 'image/jpeg' || uploadedFile.mimetype === 'image/gif') {
                    // upload the file to the /public/assets/img directory
                    uploadedFile.mv(`public/assets/img/${image_name}`, (err ) => {
                        if (err) {
                            return res.status(500).send(err);
                        }
                        // send the player's details to the database
                        let query = "INSERT INTO `players` (first_name, last_name, position, number, image, user_name) VALUES ('" +
                            first_name + "', '" + last_name + "', '" + position + "', '" + number + "', '" + image_name + "', '" + username + "')";
                        db.query(query, (err, result) => {
                            if (err) {
                                return res.status(500).send(err);
                            }
                            res.redirect('/');
                        });
                    });
                } else {
                    message = "Invalid File format. Only 'gif', 'jpeg' and 'png' images are allowed.";
                    res.render('add-player.ejs', {
                        message,
                        title: Welcome to Socka | Add a new player
                    });
                }
            }
        });
    },
    editPlayerPage: (req, res) => {
        let playerId = req.params.id;
        let query = "SELECT * FROM `players` WHERE id = '" + playerId + "' ";
        db.query(query, (err, result) => {
            if (err) {
                return res.status(500).send(err);
            }
            res.render('edit-player.ejs', {
                title: Edit  Player
                ,player: result[0]
                ,message: ''
            });
        });
    },
    editPlayer: (req, res) => {
        let playerId = req.params.id;
        let first_name = req.body.first_name;
        let last_name = req.body.last_name;
        let position = req.body.position;
        let number = req.body.number;

        let query = "UPDATE `players` SET `first_name` = '" + first_name + "', `last_name` = '" + last_name + "', `position` = '" + position + "', `number` = '" + number + "' WHERE `players`.`id` = '" + playerId + "'";
        db.query(query, (err, result) => {
            if (err) {
                return res.status(500).send(err);
            }
            res.redirect('/');
        });
    },
    deletePlayer: (req, res) => {
        let playerId = req.params.id;
        let getImageQuery = 'SELECT image from `players` WHERE id = "' + playerId + '"';
        let deleteUserQuery = 'DELETE FROM players WHERE id = "' + playerId + '"';

        db.query(getImageQuery, (err, result) => {
            if (err) {
                return res.status(500).send(err);
            }

            let image = result[0].image;

            fs.unlink(`public/assets/img/${image}`, (err) => {
                if (err) {
                    return res.status(500).send(err);
                }
                db.query(deleteUserQuery, (err, result) => {
                    if (err) {
                        return res.status(500).send(err);
                    }
                    res.redirect('/');
                });
            });
        });
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

> 这个文件处理玩家页面的所有 post 和 get 请求。添加球员功能包含将球员的图像上传到***/public/assets/img***目录并将球员的详细信息发送到数据库的功能。

## 连接路线和视图

转到 **app.js** 并取消对以下行的注释

*   在第 8 和第 9 行

```
// const {getHomePage} = require('./routes/index');
// const {addPlayerPage, addPlayer, deletePlayer, editPlayer, editPlayerPage} = require('./routes/player'); 
```

Enter fullscreen mode Exit fullscreen mode

*   在第 40 - 47 行

```
/*
app.get('/', getHomePage);
app.get('/add', addPlayerPage);
app.get('/edit/:id', editPlayerPage);
app.get('/delete/:id', deletePlayer);
app.post('/add', addPlayer);
app.post('/edit/:id', editPlayer);
*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 app

删除注释行后，检查命令提示符以确保代码没有错误，然后打开浏览器并打开 [http://localhost:5000](http://localhost:5000) 。将显示索引页面，由于没有添加球员，该页面将类似于下图:

[![](../Images/08dcb0079d9c04d2e2ce1c7089ba0a95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PfIBmWel--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1529611843/node-mysql-crud-app/homepage.png)

点击页面上的**添加玩家**链接，将加载添加玩家页面，然后填写表格添加玩家。添加一个玩家后，主页会在表格中显示添加的玩家。像这样

[![](../Images/9d5935ce752156ca16dc16dfc754f600.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9iQBVrG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1529611843/node-mysql-crud-app/homepage2.png)

如果遇到错误，请在评论区引起我的注意，或者在 [github](https://github.com/achowba/node-mysql-crud-app) 上查看该项目的报告。
代码并不完美，请随时做得更好，并发送一个拉请求。

别忘了在 [twitter](https://twitter.com/achowba_) 或 [github](https://github.com/achowba) 上关注我。