# 如何用 Node.js 和 MongoDB 构建一个 URL Shortener

> 原文：<https://dev.to/scalegrid/how-to-build-a-url-shortener-with-nodejs-and-mongodb--5gb4>

[![How to build a URL shortener with Node.js and MongoDB](img/df9c93e5ceda352f7aa5f9ad9678b848.png)T2】](https://scalegrid.io/blog/how-to-build-a-url-shortener-with-node-js-and-mongodb/)

在这篇文章中，我们将向您展示如何使用 Express.js (Node.js)和 MongoDB 构建一个 URL 缩短服务，如 [bit.ly](https://bitly.com/) 或 [goo.gl](https://goo.gl/) 。[这是我们将通过我们的](https://sg-url-shortener.herokuapp.com/) [MongoDB 托管](https://scalegrid.io/mongodb.html)平台构建的最终产品的演示。

[![How to build a URL shortener with Node.js and MongoDB](img/cc631da28f43fb41034f47e16c0b08a0.png)T2】](https://sg-url-shortener.herokuapp.com/)

## 网址缩写程序是如何工作的？

在一个非常高的层次上，URL shortener 的工作方式是获取一个输入的 URL，并创建一个相对缩短的版本，简化为一种易于共享的格式。缩短的哈希将通过对自动递增的计数器进行基本编码来生成，并创建一个最小三个字符的哈希，该哈希随着存储的 URL 数量的增加而增加。

当访问 URL 的缩短版本时，服务将解码散列以获取存储在 MongoDB 中的原始 URL，然后将您的用户重定向到它。

## 入门

以下是我们将在本教程中用来构建 URL 缩短器的技术列表:

*   #### [Express.js](https://expressjs.com/) (Node.js 后端)

    Node.js 的 web 应用程序框架。我们将使用它来构建一个 API，用于缩短 URL 并将用户重定向到原始 URL。

*   #### [MongoDB](https://www.mongodb.com/) (存储网址)

    NoSQL 数据库非常适合这种应用。它提供了灵活的模式设计，并且易于上手。在本教程中，我们将在 ScaleGrid 上使用共享的 MongoDB 集群。设置只需不到 5 分钟，您可以在这里创建一个免费的 30 天试用版[来开始使用](https://console.scalegrid.io/users/register)。

*   #### HTML、CSS、JavaScript(前端)

    HTML、CSS 和 JavaScript 将用于构建应用程序的前端，您的用户将使用它来缩短 URL。

## 网址缩写教程

1.  ### 设置 MongoDB 数据库结构

    让我们从在 ScaleGrid 上创建一个[共享 MongoDB 集群](https://help.scalegrid.io/docs/mongodb-new-cluster-shared-hosting)开始。这是创建快速集群最简单的方法，但是您也可以在您的机器上安装 MongoDB 并从那里开始。

    [![Create a MongoDB Shared Cluster: Build URL Shortener - ScaleGrid Blog](img/3d0dd1ae535943466156590053fb8c32.png)](https://console.scalegrid.io/users/register)

    一旦创建了集群，您将获得一个[连接字符串](https://help.scalegrid.io/docs/mongodb-connecting-to)，只需在集群详细信息页面上单击一下就可以复制该字符串。我们需要这个字符串从我们的应用程序连接到集群。请记住，不要与任何人共享您的连接字符串。

    我们将需要两个 URL 缩写的集合:

    *   #### **收藏 1**

        存储 URL 和动态生成的 ID 的集合:

        [![Collection 1: Store the Shortened URL and Dynamically Generated ID](img/077b20cb42c6ac22d19dd234984506d7.png)](https://scalegrid.io/blog/wp-content/uploads/2018/01/Table-Collection1.png)
    *   #### **收藏二**

        用于维护计数器的集合，当新的 URL 存储在以前的集合中时，该计数器将自动递增。用这个新增加的计数器在前一个集合中创建新文档:

        [![Collection 2: Maintain the Auto-Incremented Counter for Storing Shortened URLs](img/f0b58eda618bb57a839efcbd82adc53c.png)](https://scalegrid.io/blog/wp-content/uploads/2018/01/Table-Collection2.png)

    需要注意的是，我们没有将散列存储在数据库中的任何地方。哈希将使用通用算法进行基本编码和动态解码，这将产生存储在第一个集合中的唯一 ID。这个 ID 将获取用户将重定向到的原始 URL。

    对于本教程，我们将使用通用的 base64 编码和解码机制来生成我们独特的缩短哈希。有关使用 base64 编码/解码字符串的更多信息，请查看我们下面的 [MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/Base64_encoding_and_decoding)。

2.  ### 设置 Express.js 后端

    下面是设置 Node.js 后端所需的依赖项列表:

    *   快速(基础应用程序)
    *   body-parser(用于解析通过 HTTP 请求发送的数据的插件)
    *   btoa (base64 编码)
    *   atob (base64 解码)
    *   dotenv(出于开发目的将连接字符串存储在. env 文件中)
    *   mongose(node . js 上 MongoDB 的适配器)

    以下是 package.json 的示例版本，您可以使用它来设置应用程序:

    ```
    {
      "name": "sg-url-shortener",
      "version": "1.0.0",
      "description": "A simple URL shortener built with Node.js and MongoDB",
      "dependencies": {
        "atob": "^2.0.3",
        "body-parser": "^1.15.2",
        "btoa": "^1.1.2",
        "dotenv": "^4.0.0",
        "express": "^4.10.2",
        "mongoose": "^4.13.7"
      },
      "main": "index.js",
      "scripts": {
        "start": "node index.js"
      },
      "engines": {
        "node": "4.8.4"
      }
    }

    ```

    运行“npm install”来安装所有必需的依赖项。

    一旦我们所有的依赖项都设置好了，我们需要连接到我们共享的 MongoDB 集群。创建一个。env 文件，并向其中添加连接字符串。您可以从 ScaleGrid 控制台上 Overview 选项卡下的 Cluster Details 页面获取连接字符串。

    ```
    connectionString=mongodb://user:password@devservers
    ```

    在我们开始编写代码之前，可视化应用程序流是一个很好的做法，这样我们就可以很好地理解缩短过程是如何工作的。以下图表显示了 URL 缩短的过程:

    [![App Visualization Flow Chart: Shortening URLs - ScaleGrid Blog](img/e2087ff0e5e7df4021a032c0a13257fe.png)](https://scalegrid.io/blog/wp-content/uploads/2018/01/Process-Storing-URLs.png)

    下图显示了访问缩短的 URL 时的重定向过程:

    [![App Visualization Flow Chart: Redirecting Shortened URLs - ScaleGrid Blog](img/35560e9039d2ee1d4850ce52231e9586.png)](https://scalegrid.io/blog/wp-content/uploads/2018/01/Process-Redirecting-Users.png)

    现在我们已经可视化了整个过程，是时候把上面的流程图翻译成代码了。

3.  ### 初始化应用程序

    在开始编写业务逻辑之前，我们需要用节点模块初始化应用程序，并设置一个服务器。

    加载。仅开发模式下的 env 文件。由于演示应用程序托管在 [Heroku](https://www.heroku.com/home) 上，因此已经从 Heroku 仪表板创建了一个环境变量，其中已经包含了连接字符串:

    ```
    if(process.env.NODE_ENV !== 'production') {
        require('dotenv').load();
    }
    ```

    应用程序初始化、服务器和中间件设置。请注意，我们还从环境变量中获取连接字符串:

    ```
    var express = require('express'),
        bodyParser = require('body-parser'),
        app = express(),
        http = require('http').Server(app),
        mongoose = require('mongoose'),
        btoa = require('btoa'),
        atob = require('atob'),
        promise,
        connectionString = process.env.connectionString,
        port = process.env.PORT || 8080;

    http.listen(port, function() {
        console.log('Server Started. Listening on *:' + port);
    });

    app.use(express.static('public'));
    app.use(bodyParser.urlencoded({
        extended: true
    }));
    ```

    加载我们应用前端的基本路径:

    ```
    app.get('/', function(req, res) {
        res.sendFile('views/index.html', {
            root: __dirname
        });
    });

    ```

4.  ### 在 MongoDB 中存储 URLs】

    让我们从创建用于存储数据的集合模式开始。如上所述，我们需要两个集合:一个用于存储自动递增的计数器，另一个用于存储 URL。

    ```
    var countersSchema = new mongoose.Schema({
        _id: { type: String, required: true },
        count: { type: Number, default: 0 }
    });

    var Counter = mongoose.model('Counter', countersSchema);

    var urlSchema = new mongoose.Schema({
        _id: {type: Number},
        url: '',
        created_at: ''
    });

    urlSchema.pre('save', function(next) {
        console.log('running pre-save');
        var doc = this;
        Counter.findByIdAndUpdate({ _id: 'url_count' }, { $inc: { count: 1 } }, function(err, counter) {
            if(err) return next(err);
            console.log(counter);
            console.log(counter.count);
            doc._id = counter.count;
            doc.created_at = new Date();
            console.log(doc);
            next();
        });
    });

    var URL = mongoose.model('URL', urlSchema);

    ```

    上面的代码创建了两个集合，并为存储这些集合建立了数据库。我们还为 URL 模式使用了一个预保存钩子，因为我们需要自动递增计数器并记录 URL 创建的日期和时间。

    接下来，我们需要确保我们重新开始我们的应用程序，并且删除所有以前的条目。一旦我们重置，我们将用初始值 10，000 初始化我们的计数器，以设置 URL 缩短过程。你可以从任何值开始。这是随机选择的，将自动递增 1。

    ```
    promise = mongoose.connect(connectionString, {
        useMongoClient: true
    });

    promise.then(function(db) {
        console.log('connected!');
        URL.remove({}, function() {
            console.log('URL collection removed');
        })
        Counter.remove({}, function() {
            console.log('Counter collection removed');
            var counter = new Counter({_id: 'url_count', count: 10000});
            counter.save(function(err) {
                if(err) return console.error(err);
                console.log('counter inserted');
            });
        });
    });

    ```

    我们的应用程序现在准备开始接受和缩短网址！让我们创建一个 POST API，我们的前端将使用它来发送 URL:

    ```
    app.post('/shorten', function(req, res, next) {
        console.log(req.body.url);
        var urlData = req.body.url;
        URL.findOne({url: urlData}, function(err, doc) {
            if(doc) {
                console.log('entry found in db');
                res.send({
                    url: urlData,
                    hash: btoa(doc._id),
                    status: 200,
                    statusTxt: 'OK'
                });
            } else {
                console.log('entry NOT found in db, saving new');
                var url = new URL({
                    url: urlData
                });
                url.save(function(err) {
                    if(err) return console.error(err);
                    res.send({
                        url: urlData,
                        hash: btoa(url._id),
                        status: 200,
                        statusTxt: 'OK'
                    });
                });
            }
        });
    });
    ```

    如流程图所示，一旦收到有效的 URL，我们就检查它是否存在于数据库中。

    如果找到了，我们解码相应的 _id 字段并返回散列。我们的前端构建缩短的 URL，并将其呈现给用户进行重定向。

    如果没有找到 URL，我们在集合中保存一个新文档。请记住，每次保存 URL 时都会运行一个预保存步骤。这将自动增加计数器并记录当前日期和时间。添加文档后，我们将散列发送到前端，前端将构造缩短的 URL，并将其呈现给用户进行重定向。

5.  ### 重定向用户

    我们快完成了！一旦创建了缩短的 URL，我们需要一种方法在用户访问缩短的 URL 时重定向用户。

    ```
    app.get('/:hash', function(req, res) {
        var baseid = req.params.hash;
        var id = atob(baseid);
        URL.findOne({ _id: id }, function(err, doc) {
            if(doc) {
                res.redirect(doc.url);
            } else {
                res.redirect('/');
            }
        });
    });
    ```

    上面的代码在缩短的 URL 中查找散列，base64 解码它，检查集合中是否存在该 ID，并相应地重定向用户。如果没有找到 ID，用户将被重定向到 URL 缩写程序的主页。

    对于前端代码，请查看本文末尾提到的 GitHub 资源库。它实际上是一个文本框字段，带有一个将 URL 发送到后端的按钮，这超出了本文的范围。

# 更多网址缩写增强功能

我们完事了。我们有一个简单的网址缩写器，可以在内部用来简化你的链接。如果你想添加更多的功能，这里有一个你可以额外实现的列表:

*   更好的代码分割
*   适用于较小字符哈希(例如 base52)的更好/定制的缩短算法
*   在社交媒体上分享缩短的网址
*   一键式 URL 复制
*   自定义哈希
*   用户注册和相关缩短的 URL

完整代码可在此处获得: [ScaleGrid URL Shortener 代码示例-Github](https://github.com/Scalegrid/code-samples/tree/sg-url-shortener)Heroku 上托管了一个演示应用: [ScaleGrid URL Shortener 演示](https://sg-url-shortener.herokuapp.com/)

和往常一样，如果你做了一些很棒的东西，一定要发微博给我们。如果您在 [MongoDB](https://scalegrid.io/mongodb.html) 或 [Redis 托管](https://scalegrid.io/redis.html)和管理方面需要帮助，请通过 [support@scalegrid.io](mailto:support@scalegrid.io) 联系我们。