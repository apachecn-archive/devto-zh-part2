# 结合 MongoDB 使用 AJAX

> 原文：<https://dev.to/rmadisetti3/using-ajax-in-conjunction-with-mongodb-12n>

各位 Javascript 编码员好，

在本文中，我们将讨论如何在包含 MongoDB 数据库的全栈 web 应用程序中实现 AJAX 调用。

首先，AJAX 是 jQuery 中的一种方法，它允许一个程序同时向服务器(即 MongoDB)发送和接收数据，甚至不需要刷新页面。因此，AJAX 对于实现带有按钮或用户输入的应用程序非常重要，因为它可以用在负责向页面呈现文本或数据的函数中。

接下来，让我们在应用程序中实现 AJAX。我们需要 server.js 中的所有相关包，或者一个不同名称的文件，该文件将 mongoose 数据库连接到您的 api 和 html 路由。该文件应该类似于以下内容:

`const express = require('express');`
`const bodyParser = require('body-parser');`
T2】

`const PORT = process.env.PORT || 3000; // any port number is acceptable`

`// Initialize Express`
T1】

`// Use body-parser for handling form submissions`
`app.use(bodyParser.urlencoded({ extended: true }));`
`// Use express.static to serve the public folder as a static directory`

`mongoose.connect('mongodb://localhost/[name of application database]', { useNewUrlParser: true });`

`// Routes`
`// API Routes (require from routes folder and pass in Express app)`
`require('./routes/api-routes')(app);`
`// HTML Routes (require from routes folder and pass in Express app)`
T4】

`// Start the server`
`app.listen(PORT, function() {`
`console.log(` `App running on port ${PORT}` `);`

现在，如果还没有初步的包，就需要它们了。接下来，在不同的 js 文件中，您可以使用 AJAX 创建访问 mongoose 路由的函数，比如 GET、POST 或 DELETE。下面是一个示例，展示了如何构建一个呈现函数，从 mongoose 数据库中获取数据并将其显示到页面上:

`const render = function () {`
`$.ajax({ url: '/[put your route here]', method: 'GET' })`
`.then(function (data) {`
`let htmlstr = '';`
`data.forEach(element => {`
`htmlstr +=``<h1 class="content">${element.content}</h1>``;`
`});`
`$('#addStr').html(htmlstr);`
`})`

`.catch(function (err) {`
`console.log(err);`
`});`
`}`

在这个函数中，AJAX 调用使用数据库 GET routes，并为每个数据条目创建一个包含条目内容的 html h1 元素，并将其添加到一个空 div 中。