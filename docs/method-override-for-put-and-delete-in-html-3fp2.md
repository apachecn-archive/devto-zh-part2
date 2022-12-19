# HTML 中 PUT 和 DELETE 的方法重写

> 原文：<https://dev.to/moz5691/method-override-for-put-and-delete-in-html-3fp2>

下面是 REST 中常用的四种 HTTP 方法。GET -这将提供对资源的只读访问。
·PUT——这是为了创建一个新资源
·DELETE——这是为了删除一个资源
·POST——这是为了更新一个现有资源或者创建一个新资源。

当我们试图在 HTML 中使用 PUT 和 DELETE 时，我们遇到了一个问题。浏览器确实支持上传和删除，但只能通过 AJAX 请求，而不能通过“HTML 表单”提交。

在 HTML4 和 HTML5 规范中，HTML 表单元素应该允许的 HTTP 方法只有“GET”和“POST”。“HTML 表单”不支持 PUT 和 DELETE 没有明确的原因。我的理解是 POST 可以被更有效地重用以支持 PUT 和 DELETE 方法。PUT 和 DELETE 通常不会重定向到适当的页面，而 POST 会相应地重定向。我们可能不需要为 HTML 重新发明 PUT 和 DELETE 方法，在这种情况下只需重用 POST(这是我的观点)。

本页的目的是理解如何使用“方法覆盖”中间件来解决这个问题。以下可能是最受欢迎的方法。

首先，安装用于方法覆盖的 npm 包。

`$ npm install method-override`

这个想法是用一个头来覆盖这个方法。下面的例子是重写 POST 方法来创建 PUT 和 DELETE 方法。

指定头名为" methodOverride"
`const methodOverride = require('method-override');`的字符串参数

在请求中用“_method”标头重写。你可以选择你自己的标题。
`app.use(methodOverride('_method'));`

HTTP 表单允许使用“post”方法。我们可以为下面的“上传”和“删除”方法重写这个 post 方法。
`app.post('/ideas', (req, res) => {
//
});`

“put”方法被 post 方法重写。
`app.put('/ideas/:id', (req, res) => {
//
});`

“delete”方法被 post 方法重写。
`app.delete('/ideas/:id', (req, res) => {
//
});`

使用 HTML 表单的查询覆盖。以下为“帖”。
`<form action="/ideas/{{idea.id}}?_method=PUT" method="post">
<input type="hidden" name="_method" value="PUT">
</form>`

使用 HTML 表单的查询覆盖。以下为“删除”。
`<form method="post" action="/ideas/{{id}}?_method=DELETE">
<input type="hidden" name="_method" value="DELETE">
</form>`