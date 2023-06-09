# 在 Heroku 应用程序上更改时区

> 原文：<https://dev.to/paulasantamaria/change-the-timezone-on-a-heroku-app-2b4>

几周前我发现了 Heroku。这是一个部署你的应用程序的伟大平台，特别是因为他们的免费计划，我一直用它作为 dev env 与我的团队远程工作。

最近我开始着手一个新项目。在这个特别的项目中，我依靠**日期**进行大部分主要操作。该应用程序有一个用 Angular 5 构建的前端，和一个用 Node.js 构建的后端

在我的本地环境中，一切都运行得很好，但是当我部署到 Heroku 时，所有的日期操作都开始变得有点随机(剧透:一点也不随机)。

事情是这样的。我来自阿根廷，我将前端部署在本地服务器上，但后端在 heroku 上，新 Heroku 应用程序的默认区域是“美国”。所以基本上我的前端和后端在**不同的时区**。

这不是我第一次处理这种日期问题(在我所在的地区没有很多本地云服务)，所以我几乎立即找到了问题的根源，我想与您分享它，以防您遇到类似的问题。

幸运的是，Heroku 让我们简单地通过添加一个新的配置变量来改变我们应用程序的时区。

## 来自 Heroku CLI

1.  打开命令外壳
2.  登录 heroku
3.  导航至您的应用目录
4.  设置您的 TZ

```
heroku config:add TZ="America/Argentina/Buenos_Aires" 
```

Enter fullscreen mode Exit fullscreen mode

## 来自 Heroku 仪表盘(网络)

1.  从浏览器登录 Heroku 仪表板
2.  导航至应用程序
3.  单击设置选项卡
4.  按下“显示配置变量”按钮
5.  将键设置为“TZ”，并将值设置为您的时区(例如:美洲/阿根廷/布宜诺斯艾利斯)
6.  按添加按钮

## 我怎么知道哪个是正确的 TZ 值

点击此[链接](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)查看时区的完整列表

## 检查新的 TZ 值

从 Heroku CLI 使用以下命令检查新配置变量的值:

```
heroku config:get TZ 
```

Enter fullscreen mode Exit fullscreen mode

### 了解更多关于 Heroku 的信息

什么是 Heroku？

### 关于 Heroku 配置变量的更多信息

[配置和配置变量](https://devcenter.heroku.com/articles/config-vars)