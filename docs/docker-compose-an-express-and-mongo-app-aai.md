# docker-编写一个 express 和 mongo 应用程序

> 原文：<https://dev.to/jay97/docker-compose-an-express-and-mongo-app-aai>

Docker 是一个工具，它使您能够将您的开发环境容器化，从而帮助您更高效地专注于代码，并且不再担心如何设置您的环境来匹配您的所有队友的工作空间。

Docker-compose 是另一个与 Docker 一起工作的工具，用于将容器或服务分组，作为一个应用程序一起工作。

##### 需要安装 Docker 和 Docker-compose 才能跟随

### 首先，写一个简单的快递服务器

`index.js`

```
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const app = require('express')();

app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({ extended: false }));

app.get('/', (req, res) =>{
    res.send("it's working :)")   
})

const port = 3000;
app.listen(port, () => console.log('Server running...')); 
```

在上面的代码中，我们需要几个模块，我们稍后会用到它们。并写一个小服务器监听**到**的`/`路线。

### 接下来让我们添加数据库连接并编写模式

`index.js`

```
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const app = require('express')();

// connect to Mongo daemon
mongoose
  .connect(
    'mongodb://localhost/express-mongo',
    { useNewUrlParser: true }
  )
  .then(() => console.log('MongoDB Connected'))
  .catch(err => console.log(err));

// DB schema
const ItemSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  date: {
    type: Date,
    default: Date.now
  }
});

Item = mongoose.model('item', ItemSchema);
app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({ extended: false }));

app.get('/', (req, res) =>{
    res.send("it's working :)")   
})

const port = 3000;
app.listen(port, () => console.log('Server running...')); 
```

### 变化

1.  添加的数据库
2.  为我们的应用程序添加了模式

**注意:**我们现在连接到本地主机，但随着我们从 Docker Hub 引入 mongo `Image`，这种情况将会改变。如果你没有安装 **MongoDB** 也没关系，你不需要安装任何东西。

### 接下来让我们来简单的看一下 **`ejs`**

`/views/index.ejs`

```
<!DOCTYPE html>
<html lang="en">

<head>
  My Node App
</head>

<body>
  <h1>My Node App</h1>
  <form method="post" action="/item/add">
    <label for="name">Name</label>
    <input type="text" name="name">
    <input type="submit" value="Add">
  </form>
</body>

</html> 
```

所以，这只是一个简单的视图，显示一个表单并将表单提交给`/item/add`

### 接下来我们创建发布路线并渲染视图

`index.js`

```
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const app = require('express')();

// connect to Mongo daemon
mongoose
  .connect(
    'mongodb://localhost/express-mongo',
    { useNewUrlParser: true }
  )
  .then(() => console.log('MongoDB Connected'))
  .catch(err => console.log(err));

// DB schema
const ItemSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  date: {
    type: Date,
    default: Date.now
  }
});

Item = mongoose.model('item', ItemSchema);
app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({ extended: false }));

app.get('/', (req, res) =>{
    res.render("index");   
})

//Post route
app.post('/item/add', (req, res) => {
  const newItem = new Item({
    name: req.body.name
  });

  newItem.save().then(item => res.redirect('/'));
});

const port = 3000;
app.listen(port, () => console.log('Server running...')); 
```

现在，第一个 GET `/`路由将显示表单，POST `/item/add`路由将获取日期，在 DB 中创建一个条目并重定向回`/`。一件物品可以是任何东西，我们不在乎，因为这不是这篇文章的重点。
**也**让我们将`/view/index.ejs`改为显示`DB`中的所有项目。
`/views/index.ejs`

```
 <h4>names:</h4>
  <ul>
    <% items.forEach(function(item) { %>
    <li>
      <%= item.name %>
    </li>
    <% }); %>
  </ul> 
```

将此添加到`index.ejs`中的`Form`下

### 现在让我们潜入 docker

首先，让我们的`Dockerfile`到*从`Dockerfile`构建*一个 nodejs 映像

`/Dockerfile`

```
FROM node:10 //pull a node image from docker hub

WORKDIR /app //set the working dir to /app

COPY package.json package.json //copy package.json to the container

RUN npm install // install package.json modules in container

COPY . . //copy everything to container /app

EXPOSE 3000 //expose port 3000 to mount it to another port in local machine 

RUN npm install -g nodemon // install nodemon for changes on the fly

CMD [ "nodemon", "index.js" ] // start server inside container 
```

1.  第一行`From`拉一个 nodejs 图像
2.  第二行设置容器内部的工作位置
3.  将 package.json 复制到容器内的工作目录
4.  在容器中安装 package.json 模块
5.  将当前目录中的所有文件复制到容器中的工作目录
6.  公开端口 3000，将其映射到我们机器上的其他端口，以访问 app \
7.  在容器中安装 nodemon
8.  运行应用程序

### 接下来我们需要创建 docker compose

`docker-compose.yml`将使我们能够一起运行多个容器来构建我们的应用程序。所以我们来做一个`docker-compose.yml`文件。

`/docker-compose.yml`

```
version: "3"
services:
  app:
    container_name: express-mongo
    restart: always
    build: ./
    ports:
      - "80:3000"
    volumes:
      - .:/app
    links:
      - mongo
  mongo:
    container_name: mongo
    image: mongo
    ports:
      - "27017:27017" 
```

1.  第一行告诉 docker 使用哪个版本的 docker-compose(3 是最新的)。
2.  第二行定义了我们将拥有的服务，在本例中是(节点容器和 mongo 容器)
3.  第 3 行定义了服务名，可以是任何名称
4.  第 4 行定义了容器名
5.  第 5 行告诉 docker 容器如果有任何东西崩溃就重启(注意这只是针对 nodejs 容器)
6.  第 6 行从我们之前创建的`Dockerfile`构建 nodejs 映像。
7.  第 7 行将容器中的端口 3000 安装到我们机器上的端口 80
8.  第 8 行将本地工作目录中的所有文件绑定到容器中的工作目录，以实时查看更新和更改。

9.  第 9 行将 mongo 容器链接到节点容器，让它们一起通信

10.  Line10 创建其他服务(mongo)

11.  第 11 行定义了容器名

12.  第 12 行从 docker hub 提取 mongo 图像

13.  第 13 行将 mongo 容器中端口 27017 暴露给我们的本地机器，以便能够与 mongo 通信

### 接下来我们需要更改连接到 MongoDB 的路径

**改此**
`index.js`

```
mongodb://localhost/express-mongo 
```

到此
`index.js`

```
mongodb://mongo:27017/expressmongo 
```

这将连接到 mongo 容器暴露端口 *27017*

#### 现在真相大白的时刻

##### 在你的终端运行这个命令

```
docker-compose up
//or 
docker-compose up -d //to run docker-compose in background 
```

现在检查你的浏览器端口 80 上的应用程序。

##### 住手，打`ctl+c`

##### 如果你在后台运行它，输入`docker-compose stop`停止它。

##### 删除两个容器运行 docker-compose down

### 现在你可以将项目推送到 github，并从任何机器上下载下来，只要你有 docker 和 docker-compose，它就可以开箱即用了。

**注意:**当您从 githup 获取代码时，您需要通过执行`npm install`来安装所有的 node_modules，因为容器将在您的机器上的 node_modules 中查找 express 和所有其他模块。**如果**你不想在你的机器上安装任何节点模块，你需要把它添加到你的`docker-compose.yml`

#### 在卷下添加本

```
- /app/node_modules // now all you need to do is run docker-compose up 
```