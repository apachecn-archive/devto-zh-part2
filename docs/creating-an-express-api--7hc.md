# 创建 Express API

> 原文：<https://dev.to/tailomateus/creating-an-express-api--7hc>

首先，前端总是吸引我的注意力，我认为我更擅长它。然而，我冒险进入了原力的黑暗面(后端)，我可以说，在许多情况下，这不是一件好事。

我的目标不是告诉你哪种语言或框架是好是坏，而是每个人都有自己的偏好，如果你不喜欢某项技术，你没有错。

在过去的五年里，我学习或使用了 Java、C#、Zend Framework。我承认，我对配置环境和构建可见的东西所花费的时间感到失望。

除了这些技术，我还尝试了一些更敏捷的东西，所以我试着学习 Ruby on Rails 和 Laravel，虽然我很欣赏它们，但我意识到，在那一刻，我仍然没有找到适合我的框架和 lan。尽管有挫折，我没有停止参与一些项目或学习一些新的东西，所有获得的知识我将在其他时间重用。虽然我没有找到后端语言和框架，但我会继续研究前端。

一段时间以来，我开始研究 Node.js 和 T2 Express 框架，以及如何在几个步骤中创建一个 API。据 Express 自己的网站是 Node.js 的快速、灵活、极简的 web 框架。

注意:如果您不知道 NPM，请从下面的链接开始:

[你的第一个 Node.js 包](http://nodesource.com/blog/your-first-nodejs-package/)

**步骤 1 —安装 Express**

```
npm install express –-save 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 2-创建 server.js 文件**

在代码的开头导入了`express`模块并创建了一个`app`。在它基于 HTTP 方法创建路由之后。`res`和`req`对象由节点提供，相当于一个 HTTP 请求和一个响应。最后，我告诉创建的实例监听端口 8080。

```
const express = require('express');
const app = express();

app.get('/', function (req, res) {
 return res.send('Hello world');
});

app.listen(process.env.PORT || 8080); 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 3 —启动快递服务器**

如果一切顺利，只需转到 localhost: 8080。

```
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您愿意，可以使用 [nodemon](https://github.com/remy/nodemon) 来自动化这个过程。

显然，有了 Node。Js 和 Express 可以让你做几件很酷的事情，引入这个主题正是为了让你多研究，不要害怕学习新的:D 东西

**结论**

正如我在文章开头提到的，人们有不同的偏好，对我来说，快速掌握一项技术是必不可少的，小奖励激励我继续学习。

此外，确保测试一些新的东西或参与一些他们正在使用的项目。对某些人来说有好事，对其他人来说有坏事。简单！

**阅读更多**

[在 10 分钟内构建 Node.js RESTful APIs】](https://www.codementor.io/olatundegaruba/nodejs-restful-apis-in-10-minutes-q0sgsfhbd)

[用 Express 构建 node . js REST API](https://medium.com/@jeffandersen/building-a-node-js-rest-api-with-express-46b0901f29b6)