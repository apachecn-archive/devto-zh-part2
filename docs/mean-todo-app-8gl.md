# 意味着待办事项应用程序

> 原文：<https://dev.to/aveeksaha/mean-todo-app-8gl>

让我们探索 CRUD 和使用平均堆栈创建网站。

# 简介

对于那些没有听说过的人来说，MEAN stack 是一个免费的 JavaScript 软件栈，用于构建现代 web 应用程序。这个堆栈由–MongoDB、Express、Angular 和 NodeJS 组成。

*   MongoDB 是一个高度可伸缩的 NoSQL 数据库。
*   在这种情况下，Express 是一个处理路由的中间件库。
*   AngularJS 是一个用于创建 UI 的前端 JavaScript 库。
*   NodeJS 是一个 JavaScript 运行时环境，它在浏览器之外执行 JavaScript 代码，它允许我们在服务器上运行 JavaScript。

# 积垢操作

在它的核心，应用程序所做的是创建、读取、更新和删除数据库中的文档。应用程序的其余部分简单地提供了一个接口，包括前端和后端，以便于实现这一点。Mongoose 库已经用于与 MLabs MongoDB 数据库进行交互。

使用 Express，我们为 CRUD 操作设置路由，使用 get 方法读取现有 Todos 的列表。

```
app.get('/api/todos', (req, res) =>{
    Todo.find((err, todos) => {
        if(err) res.send(err);

        res.json(todos);
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

创建新待办事项的 post 方法。

```
app.post('/api/todos', (req,res) =>{
    Todo.create({
        text: req.body.text,
        done: 'false'
    }, (err, todo) =>{
        if(err) res.send(err);

        Todo.find((err, todos) => {
        if(err) res.send(err);

        res.json(todos);
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

删除特定待办事项的删除方法。对于 delete 函数，我们将要删除的 todo 的 ID 作为参数传递。

```
app.delete('/api/todos/:todo_id', (req,res) =>{
    Todo.remove({
        _id: req.params.todo_id
    }, (err, todo) =>{
        if(err) res.send(err);

        Todo.find((err, todos) => {
        if(err) res.send(err);

        res.json(todos);
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的前端，我们有一个计数器显示要做的任务数量。这可以通过 get 函数简单地计算响应中元素的数量来找到。下面是 todo 列表，通过在数据库中查询 todo 集合中的所有文档来获取这些列表。这作为一个数组传递给一个 Angular 变量，我们遍历它来创建 todos 列表。

这个列表中的每个 todo 都有自己唯一的 id，当插入新文档时，会在 mongodb 中自动创建这个 id。每个任务的文本内容旁边都有一个删除按钮。按下这个按钮时，todo 的 id 作为参数传递给 delete 方法，用于删除特定的 todo，并更新列表。

有一个用户可以输入新任务的表格。提交该表单时，用户输入的文本将作为参数发送到用于创建新待办事项的 post 方法。这个函数反过来在数据库中创建一个新文档，待办事项列表用新任务更新。

# 代号&演示

如果你想要这个项目的代码，叉这个 GitHub repo - [`MEAN Todo app`](https://github.com/Aveek-Saha/MEAN-todo-app)

如果你想看现场演示，请点击这里- [`Live demo`](https://todo0.herokuapp.com/)