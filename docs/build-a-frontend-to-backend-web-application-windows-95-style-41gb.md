# 构建前端到后端的 web 应用程序(Windows 95 风格)

> 原文：<https://dev.to/annlin/build-a-frontend-to-backend-web-application-windows-95-style-41gb>

[https://www.youtube.com/embed/IDYRlkEoK4s](https://www.youtube.com/embed/IDYRlkEoK4s)

# 看，马，我是满栈。

今天我开始玩数据库。我在[https://dev.to/aspittel/objection-knex-无痛-PostgreSQL-in-your-node-app-6n 6](https://dev.to/aspittel/objection--knex--painless-postgresql-in-your-node-app--6n6)找到了一块宝石。@AliSpittel 你是 dah MVP。你应该先去看一下[教程](https://dev.to/aspittel/objection--knex--painless-postgresql-in-your-node-app--6n6)，它很好地涵盖了 objection.js + knex。

## objection . js+Express = Web App

### 免责声明

这是初学者开始 fullstack 的尝试。我想保持简单。没有网络包，没有巴别塔，什么都没有。越香草越好。

我为我的数据库选择了`Postgres/Knex/Objection`，为后端选择了`Express`，为前端选择了普通的`HTML`、`CSS`和`JavaScript`。我是一个简单的女孩。

这个项目就像一个随机涂鸦的过程。预计代码中会出现混乱。

# 在我们开始之前，

下面是[演示](https://live-a-life-you-will-remember.herokuapp.com/)和[代码](https://github.com/linxea/life)。

## 用户需求

作为一个人，我需要一个地方记下我每天做的事情，以提醒自己除了睡觉，我已经浪费了多少时间。

哦，妈的，我太喜欢写废话了。]

# 1。设计

这次我不想关心前端。我在寻找一个 MVP，一个最小可行的产品。

但是我们需要设计数据库模式。因为我们希望将每天作为一个实体来记录，所以我们将创建一个名为`Day`的模型，其字段如下:

```
{
    "id": 1,
    "exercise": false,
    "achievement": "I started dev.to 🤗.",
    "demo_link": "http://dev.to/linxea",
    "mood": "Good vibes",
    "woke_up_at": null,
    "slept_at": null,
    "created_at": "2018-05-28T16:00:00.000Z"
} 
```

# 2。发展

### 设置

在这里下载[示例代码。](https://github.com/linxea/life)。

将这些安装在终端:

```
brew install psql
brew install knex 
```

### 数据库

通过运行命令:
在本地创建一个名为`life`的数据库

```
createdb life 
```

创造。env in folder 通过运行命令:

```
npm run create:env 
```

打开`.env`文件，用自己的用户名/密码/数据库名更新`DATABASE_URL`变量。它应该是这样的:

```
PORT=8000
APP_ENV=local
DATABASE_URL=postgres://username:password@localhost:5432/life-database 
```

方案在迁移文件夹中的 knex 生成文件中定义，该文件由命令`knex migrate:make create_days`创建。我已经在`/deploy/db/migrations`中创建了一个名为 TIMESTAMP_create_days 的。通过运行命令:
迁移迁移文件夹中的所有内容

```
knex migrate:latest 
```

要从`/deploy/db/seeds`向数据库提供默认信息，运行命令:

```
knex seed:run 
```

### 服务器/客户端

安装依赖项:

```
npm install 
```

运行该死的应用:

```
npm run start 
```

是的，我复制了我的[自述文件](https://github.com/linxea/life)中的内容。

## Optional

如果您的数据库出错，或者如果您想要重置数据库内容，您可以删除数据库，并通过运行命令:
重新执行所有操作

```
dropdb life
createdb new-life
// Remember to update the `database_name` in .env file
knex seed:run 
```

# 3。部署

您需要为此 web 应用运行服务器。Heroku 提供了一种免费且简单的方式来托管节点应用。(但愿 firebase 主机也能做到。T.T)但是，免费账号意味着服务器在 30 分钟不活动后会进入睡眠状态。所以幸运的第一个人的第一次加载时间总是很长。不是我的应用程序性能很差。

您可以创建一个免费的 AWS 帐户，在 [RDS](https://aws.amazon.com/rds/) 创建一个数据库。(我其实在我的视频，README，所有的东西里漏掉了这一部分。opps)我是 RDS 的新手，连接到数据库的响应时间需要一段时间。有时，由于响应时间长，我也无法访问数据库。:<

### 部署到 Heroku

您还可以使用这些命令轻松地部署到 Heroku(Heroku 太棒了！):

```
brew install heroku
heroku login
heroku create you-can-name-anything-you-want-here
git push heroku origin
heroku open 
```

跟随我在[https://twitter.com/linxea_](https://twitter.com/linxea_)
我打算做一个视频像凯西 Neistat 和 Nas Daily 一天，但我花了太长时间在一个视频上，waaaaaaaay 太长了。时间