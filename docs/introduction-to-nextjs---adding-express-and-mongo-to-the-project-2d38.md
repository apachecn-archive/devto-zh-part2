# Next.js 简介——向项目中添加 express 和 mongo

> 原文：<https://dev.to/aurelkurtula/introduction-to-nextjs---adding-express-and-mongo-to-the-project-2d38>

欢迎来到我探索 next 的第三部分。在第一部分中，我讲述了基础知识:如何构建和设计一个基本的网站。在[第二部分](https://dev.to/aurelkurtula/introduction-to-the-basics-of-nextjs---part-two-pad)中，我介绍了我们如何从 API 获取数据，并将其注入 react 组件生命周期。

今天我想在项目中加入 express 和 mongo。这意味着在同一个项目中，next.js 允许我们以一种非常简单的方式运行 express 和 react。

## 我们开始吧

正如您将要看到的，我们将要编写的大部分代码都是纯 express 的东西。我已经介绍了如何用 react 和 mongodb 构建 restful API，所有这些都是完全相同的事情。

让我们安装这部分教程所需的软件包

```
npm i -S express mongoose body-parser 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们从创建`./server/index.js`页面开始，添加下面的代码

```
const express = require('express')
const next = require('next')
const bodyParser = require('body-parser')
const PORT = process.env.PORT || 3000
const dev = process.env.NODE_DEV !== 'production' //true false
const nextApp = next({ dev })
const handle = nextApp.getRequestHandler() //part of next config
const mongoose = require('mongoose')

const db = mongoose.connect('mongodb://localhost:27017/Photos')

nextApp.prepare().then(() => {
    // express code here
    const app = express()
}) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的项目中添加 express 就是这么简单。

只有四行代码与 next.js 相关，其他都是常见的 express 代码:

1.  需要下一个。
2.  通过让它知道我们正在使用哪个环境来初始化它(我选择调用那个`nextApp`并调用 express 实例`app`--因为我可以在接下来的几个月内返回并毫无问题地读取 express 代码。)
3.  下一步需要`getRequestHandler()`
4.  然后我们在`nextApp`上运行`prepare`，我想这准备好了一切(！)和`then`我们经营快递。

让我们添加组成`./server/index.js`
的剩余代码

```
nextApp.prepare().then(() => {
    // express code here
    const app = express()
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: true }));
    app.use('/api/photos', require('./routes/index')) 
    app.get('*', (req,res) => {
        return handle(req,res) // for all the react stuff
    })
    app.listen(PORT, err => {
        if (err) throw err;
        console.log(`ready at http://localhost:${PORT}`)
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍，这没什么新鲜的，你可以在这里学习如何建立一个快递站点。然而，唯一重要的是`app.get('*', ...)`中的开始。这只是说所有没有被指定的路由器，让 next.js 处理它们——当然，反过来，它让我们在 react 代码中处理它们。

让我们在`./server/routes/index.js`
创建 API 路由

```
const express = require('express')
const router = express.Router()
const Photos = require('../models/photoModel')

router.get('/', (req, res) => {
    Photos.find({}, (err, photos) => {
        res.json(photos)
    })
})
router.use('/:id', (req, res, next) => {
    console.log(req.params.id)
    Photos.findById(req.params.id, (err, photo) => {
        if(err)
            res.status(500).send(err)
        else 
            req.photo = photo 
            next()
    })
})
router
    .get('/:id', (req, res) => {
        return res.json( req.photo )
    })
    .put('/:id', (req, res) =>{
        Object.keys(req.body).map(key=>{
            req.photo[key] = req.body[key]
        })
        req.photo.save()
        res.json(req.photo)
    })
module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

上面我们基本上创建了我们知道 react 代码需要的 API 点。当请求`/api/photos`时，我们返回在数据库中找到的所有照片。如果在`/api/photos/:id`上发出任何类型的请求，那么我们必须在数据库中找到相关的对象。然后，根据请求是一个`get`还是一个`put`，我们或者用对象来响应，或者根据我们收到的所需数据来编辑对象。

最后我们需要创建`./server/models/photoModel.js`

```
const mongoose = require('mongoose')
const schema = mongoose.Schema

const photoModel = new schema({
    tagline: { type: String} ,
    image: { type: String},
    likes: { type: String},
    comments: { type: Array, default: [] }
})

module.exports = mongoose.model('photos', photoModel) 
```

Enter fullscreen mode Exit fullscreen mode

也就这样了！

## 运行 app

与之前的教程不同，现在我们想从`./server/index.js`
开始运行整个应用程序

```
node server/index.js 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果你跟着做，你还需要做两件事:

在 react 代码中，`/pages/index.js`和`/pages/photo.js`我们需要编辑地址，现在我们需要指向`3000/api/photos`。同样，在获取时，我们会将`id`改为`_id`。当然，我们使用的数据库必须包含数据。您可以通过运行以下命令来导入我留在库中的数据:

```
mongoimport --jsonArray --db Photos --collection photos --file '/your-project-folder/data/db.json' 
```

Enter fullscreen mode Exit fullscreen mode

它将数据导入到`Photos`数据库，并放在一个名为`photos`的集合中。

项目现在应该通过使用 express 作为起点来运行。因此你会运行

```
node server/index.js 
```

Enter fullscreen mode Exit fullscreen mode

由于我们配置代码的方式，next 也接管并运行 react 代码。因此，我们所拥有的是，`/api`中的任何内容都由 express 处理，而其他所有内容都由 react 处理。

开发应用程序时，运行`nodemon server/index.js`会更有用。`nodemon`监控您的代码，并在您更改代码时重启服务器。非常有用。但是，当您修改 react 代码时，您不希望它重新启动服务器。在其他事情中，它可能会陷入一个循环。下一次重新加载反应，然后`nodemon`由于文件改变重新加载服务器。要解决这个问题，你应该创建一个名为`nodemon.json`的文件夹，并添加以下内容:

```
{  "verbose":  true,  "ignore":  ["node_modules",  ".next"],  "watch":  ["server/**/*"],  "ext":  "js json"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们基本上是告诉`nodemon`关注服务器目录中的 javascript 和 JSON 文件，尤其是忽略指定的文件夹。

`nodemon`是一个非常有用的包，你应该把它作为一个全局包来安装(`npm install -g nodemon`)。`nodemon`主要是监控你的节点代码，并在文件改变时重新运行服务器。

## 结束。

这就是这个教程，希望你喜欢它。在这个系列中，我想介绍的内容不多，所以请继续关注，感谢您的阅读！这部分代码的存储库可以在标记为 [part3](https://github.com/aurelkurtula/basics-of-nextJS/tree/part3) 的分支中找到

第一部分:

[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## Next.js 基础介绍

### aurel Kurt ula Aug 22 ' 187min read

#nextjs #node #react #tutorial](/aurelkurtula/introduction-to-the-basics-of-nextjs-1loa)

第二部分:

[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## Next.js 简介——从 API 获取数据

### aurel Kurt ula Aug 24 ' 185 分钟阅读

#nextjs #node #react #tutorial](/aurelkurtula/introduction-to-the-basics-of-nextjs---part-two-pad)