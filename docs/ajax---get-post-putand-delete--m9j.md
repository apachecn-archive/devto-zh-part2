# AJAX——获取、发布、上传和删除

> 原文：<https://dev.to/gyi2521/ajax---get-post-putand-delete--m9j>

大家好，大约一个月前，我写了博客“什么是 AJAX”[https://dev.to/gyi2521/what-is-ajax-34c2](https://dev.to/gyi2521/what-is-ajax-34c2)]之后，我一直在考虑如何立即写 AJAX 调用；然而，我一直在忙着我的第一个小组项目，“InstagramClone”，来自我的新兵训练营，所以我在这里..一个月后编写 AJAX 的第二部分..；)
好事情是我可以从我的项目中使用 AJAX 调用示例。

在我们的“InstagramClone”项目中，我们在客户端使用 jQuery 的 AJAX“GET”方法从后端检索照片。

```
 $.ajax({ url: 'api/photos', method: 'GET' })
            .then(function (data) {
                let content = '';
                if (data != '') {
                   data.forEach(e => {
                   content += `some code`;
 code continues...

```

我们的 GET 方法以' $ '开头。“ajax”后跟一个具有两个属性的对象，这两个属性是 url 和方法。Url 字段用将从后端提供所需数据的 API 地址填充，方法字段用“GET”填充。下一行。“then(function(data))”是从 ajax 调用中检索到数据后执行的进程。在我们的例子中，我们遍历数据并在屏幕上显示照片。

现在，让我们看看 AJAX POST(=create new)方法。下面是我们用来给照片添加评论的代码。

```
$.ajax({ url: '/api/comments', method: 'POST', data:allComment})
            .then(function () {
                $(`#${photoid}_divForComments`).append($(`            
                Instagram_Clone ${theComment}`));
            });

```

代码类似于 GET 方法，但是我们为方法字段填充了“POST ”,还添加了包含新创建的用户注释的数据。

PUT(=update)方法实际上与 POST 方法相同，如下所示:

```
$.ajax({ url: '/api/comments', method: 'PUT', data:likes})
            .then(function() {
                some code...
            });

```

对于 DELETE 方法，我们传入 photoId 进行删除。

```
        $.ajax({ url: `/api/photo/${$(this).attr('photoId')}`, method: "DELETE" })
            .then(function (data) {
                some code...
            })
            .catch(function (err) {
                some code...
            });

```

在上面的例子中，我们只是添加了。catch(function (err)'来获取错误消息(如果有的话)，您还可以将它添加到所有其他调用中。

使用 AJAX，我们可以简单地更新网页的各个部分，而不需要重新加载整个页面，这使得这个过程更快，对用户的响应更快。

感谢阅读！