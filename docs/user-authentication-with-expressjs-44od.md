# 使用 Express.js 进行用户验证

> 原文：<https://dev.to/jakesweb/user-authentication-with-expressjs-44od>

制作基于 Express.js 的应用程序的一部分是能够认证用户。认证后的问题是如何处理它们？

我目前的策略是使用一个用秘密散列加密的客户端 cookie。为了创建一个会话，我同时使用了`cookie-parser`和`express-session`模块。我用`app.js`中的`app.use`将它们链接到我的应用程序。

```
const cookieParser = require('cookie-parser');
const session = require('express-session');

app.use(cookieParser());
app.use(session({
    secret: application_secret,
    resave: false,
    saveUninitialized: false
}); 
```

使用该设置，我们可以创建与`req.session`的会话。使用用户路由器上的登录路由，我们可以设置会话。

```
router.post('/login', (req,res) => {
    // login the user and return the user object
    // if the login is successful
    req.session.email = user.email;
    req.session.role = user.role
} 
```

这会将电子邮件和角色设置为用户会话。到目前为止，我定义了三个角色:学生、教师和管理员。我现在有两种方式来使用这些角色。在 routes 中，我可以运行一个 if 语句来呈现不同的页面，或者向模板传递不同的属性。

```
if (req.session.role === 'student') {
    res.render('studentpage');
} else if (req.session.role === 'admin') {
    res.render('adminpage');
} else {
    res.render('unauthenticated');
} 
```

这不是最相关的例子。您通常会检查用户是否经过身份验证，如果经过身份验证，则呈现一个页面，如果没有经过身份验证，则进行重定向。另一种选择是采用中间件。在我的中间件目录中，我有`admin-auth.js`，它定义了一个中间件。

```
module.exports = function(req,res,next) {
    if (req.session.role === 'admin') {
        next();
    } else {
        res.render('unauthorized');
    }
} 
```

然后，在我想检查中间件是否有管理员身份验证的路线上。中间件将在路由被允许继续处理之前进行处理。

```
const authAdmin = require('../middlewares/auth-admin');

router.get('/admin', authAdmin, (req,res) => {
    res.render('admin');
}); 
```

这将首先把请求传递给中间件。中间件检查用户会话中的角色，如果角色是管理员角色，它允许路由继续进行`next()`。如果用户没有角色或者角色不是管理员角色，则 else 路径会将其更改为未经授权的网页。