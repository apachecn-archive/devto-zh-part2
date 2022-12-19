# 节点。JS 和 AJAX

> 原文：<https://dev.to/moz5691/nodejs-and-ajax-4jb5>

REST 定义了一组创建 HTTP 服务的约定。基本的四种方法可以创建 CRUD(创建、读取、更新、删除)
POST:创建资源
GET:读取资源
PUT:更新资源
Delete:删除资源

在前端，你可以以表单输入为例进行 CRUD。以下是 POST 的一个例子。这种情况非常简单，只需从前端用 HTML 运行 POST 方法。

```
///HTML Front-end: 

{{<form action="/api" method="post">
    <input type="text" class="form-control" name="text">
    <button class="btn btn-default" type="submit">
</form>}} 
```

```
/// NODE.JS Back-end:
router.post('/api', (req, res) => {
    const todo = new Todo({
    text: req.body.text,
    completedAt: Date.now()
    });
    todo
    .save()
    .then(todos => res.redirect('/'))
    .catch(err => res.status.send(err));
}); 
```

但是，如果你没有表单输入或者表单输入可能采用不同类型的方法，你可能需要采用不同的方法，比如 AJAX。

下面是一个 AJAX 的例子。结合 HTML 和 AJAX 是前端操作而节点。JS 带 DB(如 MongoDB)是后端操作。

(1)获取按钮点击事件，并从“数据标识”中获取属性。

(2)运行 AJAX 删除方法。您应该确保它方法和 URL 在 AJAX 端和节点之间正确匹配。JS + DB 端。

(3)我尝试在后端使用 RES . redirect(‘URL’)进行重定向，但是没有成功。我了解到你不能对 AJAX 调用发送重定向响应，但是你应该让浏览器重新加载页面。“window.loaction.reload()”完成了这里的工作。

```
/// Front-end : HTML side: 
<button class="btn-danger" id="delete-btn" data-id="{{this._id}}">X</button> 
```

```
///Front-end: AJAX side; 
$('.todo-list-group').on('click', '#delete-btn', function(event) {
    event.preventDefault();
    const id = $(this).attr('data-id');
    $.ajax({
        url: '/api/delete/' + id,
        method: 'DELETE',
        data: { id: id }
        }).done(function(res) {
            if (res.success) {
            console.log('id from ajax call is', res);
            window.location.reload();
        } else {
            console.log('error...ajax');
            }
});

/// Back-end: Node.js + Mongoose (MongoDB)
router.delete('/api/delete/:id', (req, res) => {
    Todo.deleteOne({ _id: req.params.id })
    .then(() => {
        res.json({ success: true });
    })
    .catch(err => {
        res.status.json({ err: err });
    });
}); 
```