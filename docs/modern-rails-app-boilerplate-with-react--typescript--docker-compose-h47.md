# 带有 React + TypeScript + Docker Compose 的现代 Rails 应用程序样板

> 原文：<https://dev.to/ohbarye/modern-rails-app-boilerplate-with-react--typescript--docker-compose-h47>

## TL；速度三角形定位法(dead reckoning)

**下面是一个刚刚在 Ruby on Rails+react . js+TypeScript+Docker Compose 上搭建的自举 app**:[https://github . com/ohbarye/Rails-react-TypeScript-Docker-example](https://github.com/ohbarye/rails-react-typescript-docker-example)

有了这个样板，你就可以快速开始构建你自己的应用了。

## 动机

如今，我觉得我们需要一个对 web 开发的广泛认可，即使我们称自己为“后端开发者”或“前端开发者”。

就我的经验而言，我是一名 Rails 工程师，但最近我的工作有点像前端开发人员，因为我把所有的工作时间都花在了用 React + TypeScript 构建 SPA(单页应用程序)上。

当然，SPA 有一个后端 API，在我的例子中是连接 PostgreSQL 的 Ruby on Rails。我使用 Docker Compose 来定义和运行多容器 Docker 应用程序，因为引导所有的应用程序和中间件并不简单。

学习每项技术本身并不是负担。我相当喜欢学习。但是我想我会继续我的游戏，它的技术和我在工作中开发的几乎一样。

## Rails-React-TypeScript-Docker 示例

因此，我用下面的现代 web 技术栈构建了一个示例应用程序。

[https://github . com/ohbarye/rails-react-typescript-docker-example](https://github.com/ohbarye/rails-react-typescript-docker-example)

*   红宝石 2.5.1
*   [轨道](https://rubyonrails.org/) 5.2.0
*   React.js 16.4.1
*   打字稿 2.9.2
*   [码头工人](https://docs.docker.com/)
*   [PostgreSQL](https://www.postgresql.org/)

## 用法

```
$ git clone https://github.com/ohbarye/rails-react-typescript-docker-example.git && cd rails-react-typescript-docker-example

# Setup
$ docker-compose run frontend yarn
$ docker-compose run backend rake db:create

# Start
$ docker-compose up -d
$ open http://localhost:3000 
```

Enter fullscreen mode Exit fullscreen mode

一旦我有了一个操场，就该在地基上建点什么了。我希望这能帮助其他和我有同样担忧的开发者。