# 可居住的

> 原文：<https://dev.to/himanshuc3/entry-livetable-5h3o>

# 我造了什么

[https://www.youtube.com/embed/VQveIMl4fwQ](https://www.youtube.com/embed/VQveIMl4fwQ)

这是一个公共休息室时间表建设 PWA，其中有共同问题要解决的人可以决定如何同步分配时间。例如，假设一个补习小组有一组有不同课表的学生，他们试图设定上共同补习课的最佳时间。他们可以使用这个应用程序，在那里建立自己的房间，然后安排他们的时间表。我打算建立更多的功能，但现在由于期末考试，我担心这是我的条目。

## 演示链接

项目部署在 heroku:[https://mysterious-beach-92009.herokuapp.com/](https://mysterious-beach-92009.herokuapp.com/)

*注*:响应设计、UI 设计、加载动画、与时间表操作相关的自定义特性到目前为止还没有添加。

## 链接到代码

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) [【喜马拉雅山 3】](https://github.com/himanshuc3)/[活表](https://github.com/himanshuc3/LiveTable)

### 实时时间表安排。自由职业者也可以使用:)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 可居住的

### 实时时间表安排。可以被自由职业者使用。

## 部署于:[https://mysterious-beach-92009.herokuapp.com/](https://mysterious-beach-92009.herokuapp.com/)

# 如何投稿

1.  派生并克隆项目。
2.  安装依赖项。有两个 package.json 文件——一个用于 node/express server，一个用于 react app。`npm i`在根文件夹中。然后`cd`进入客户端，再`npm i`打包。
3.  在 cmd 上运行`npm run dev`以同时运行 express server 和 react server 隐含代理。

注意:react server 运行在 localhost:3000 上，express server 运行在 [http://localhost:5000](http://localhost:5000) 上。

# 技术堆栈

### 反应，表达。

### Redux、cors、redux-thunk 等。

</article>

[View on GitHub](https://github.com/himanshuc3/LiveTable)

*注意*:大量的重构工作有待完成！！！

## 我是怎么建的(栈是什么？在这个过程中，我遇到了问题或发现了新的东西吗？)

MERN 堆栈:

前端- React
前端库- moment、cleave.js、nuka carousel、rodal、react-timetable-events
后端- Node、Express
通过 pusher 实现实时通信😍

*注意* : Mongo 现在还没有集成，因为现在它是一个演示应用，我也没有太多时间。但肯定会有 OAuth 和 mongo 加入进来。

### 我遇到的问题:

1.  设置 MERN 堆栈:我以前使用过 React 和 Express。但没有结合。一开始，管理两个不同的服务器，一个用于 react，一个用于 Express 有点棘手和具有挑战性，但是我克服了它。感谢这个挑战。
2.  研究:我首先尝试构建自己的调度程序。失败了。很快又试了一个。失败了。又尝试了 3 次后找到了正确的库。经验:研究节省了大量时间。
3.  推手:真的很难.....不，我开玩笑的。推动器顺利集成。几乎没写 5-6 行集成。

# 附加资源/信息

其他资源:

1.  在开发过程中创建了 undraw.co 网站。那里有一些非常可爱的插图。
2.  提出一个想法比我想象的要容易。一些吻痕，但我会张开双手去拿。