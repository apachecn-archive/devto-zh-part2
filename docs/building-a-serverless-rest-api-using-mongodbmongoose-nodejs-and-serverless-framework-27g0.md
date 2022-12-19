# 使用 MongoDB(mongoose)、NodeJs 和无服务器框架构建无服务器 REST API

> 原文：<https://dev.to/delimanicolas/building-a-serverless-rest-api-using-mongodbmongoose-nodejs-and-serverless-framework-27g0>

# 目标

在本地构建和测试一个无服务器 API，它可以使用 mongoose 访问本地 MongoDB

# 要求

好了，伙计们，首先我需要解释我创建这个帖子的原因。我是一个年轻的后端开发人员，我遇到了很多关于创建无服务器 API 的信息，但是我意识到使用 mongoose 构建和本地测试的文章非常缺乏。无论如何，这是一个相当简单的教程，但如果你像我昨天一样，也许它可以节省你一些时间。

这一课是专门针对那些已经知道如何设置这个 API 的人的，但是因为两三件解释不清楚的事情而感到痛苦。因此，如果你不知道如何设置你的 AWS 帐户，或者不知道如何使用无服务器框架，这里有一些视频可以帮助我:
[如何创建和部署一个简单的无服务器 API](https://www.youtube.com/watch?v=71cd5XerKss&t=322s)
[多了解一点关于 AWS LAMBDA](https://www.youtube.com/watch?v=PEatXsXIkLc&index=2&list=PLWYz5kbNpmYVOHUtCwWkRsDtjACI4NfGb)
[安装 MongoDB](https://www.youtube.com/watch?v=WH5GgHaEy7E&t=360s)

# 开工吧！

### 好了，让我们开始安装无服务器软件包:

```
npm install -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
现在让我们使用 aws-nodejs 模板创建一个默认服务

```
sls create -t aws-nodejs -p api && cd api 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
现在你可以看到在我们刚刚创建的名为“api”的文件夹中，我们有两个文件:“handler.js”和“server less . yml”

### 现在，在您的 api 文件夹中，让我们初始化 npm

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
现在，让我们安装猫鼬

```
sudo npm install mongoose --save 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
好了，伙计们，在上面的“需求”部分，我推荐了这个[视频](https://www.youtube.com/watch?v=71cd5XerKss&t=322s)，它将解释我们的文件中的基本功能是如何工作的，如果你们觉得有必要，可以再看一遍，因为我们将对那些文件进行一些修改。

### 首先，确保运行“mongod”命令，因为我们需要一个本地 MongoDB 服务。

### 好的...现在，让我们在我们的 api 目录中创建一个新的文件夹，命名为“Schemas”，在里面我们将为我们的 MongoDB 创建一个新的模式，如果你不习惯使用模式，请查看这个 mongose[文档](http://mongoosejs.com/docs/guide.html)

```
(inside our directory named 'api') mkdir schemas 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
然后

```
(inside schemas) touch user.js 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
使用一些编辑器(我喜欢 Atom)，编辑一个 var 里面的 user.js 结构，用这个导出一个模块

[![Just like this](img/e2c3fabef65e19e6118b9e19570b07bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pWCDyAFS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/98lp77l2rtekr7t78cor.png)

### 好了，现在在“handler.js”内部让我们修改同一个“hello”函数来连接我们正在运行的 Mongod 服务并执行一个简单的查询，但是在我们需要导入“mongoose”之前，启动一个连接，获取我们的 userSchema 并创建一个模型。

```
(inside handler.js) var mongoose = require("mongoose"); 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
然后

```
var connectorMongodb =  mongoose.connect('mongodb://localhost/mynewDB'); 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
差不多了...

```
var userSchema = require('../api/schemas/userSchema.js'); 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
没事吧...

```
var userModel = mongoose.model('user',userSchema,'user'); 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
为了记住你们，这一课不是为所有初学者准备的，所以如果你不知道如何使用 mongoose 以及什么是模型或模式，先去学习一下吧。这一课是专门为那些因为缺乏使用 mongoose 创建 Serverles Rest API 的信息而陷入困境的人准备的。

### 好了，现在让我们在我们的“handler.js”中应用一些改变，并且记住一旦我们的 mongoose 连接打开，我们就必须关闭它，否则我们的函数将“超时”(Lambda 函数有一个默认的秒数，直到它停止)

[![Like this](img/82e7b14bd31b8dcb9d370742f5139e20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CF8xqaAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u9mxbb8una6f7rkzt536.png)

### 现在，在您的 api 目录中，运行这个命令，以便在本地测试您的函数“hello”

```
sls invoke local --function hello 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
应该会返回一个 Json，里面有你用户的收藏信息

### 我猜你的 DB 里面应该已经存储了一些用户数据，只要去 mongoshell 使用 db.user.insert 就可以了

# 希望对你们有所帮助，回头见。

[我的 Instagram 如果你想和我讨论任何事情](https://www.instagram.com/delimanicolas/)