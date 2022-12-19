# 在节点应用程序中使用 instagram api 和 passports

> 原文：<https://dev.to/aurelkurtula/working-with-instagram-api-and-passportjs-in-a-node-application--5068>

使用第三方认证的通常目的，除了大多数用户现在期望它的事实之外，是你不需要处理新用户的注册。

当他们到达您的应用程序时，用户可以使用他们首选的社交媒体帐户验证他们自己，这样他们就向您提供了一些关于他们自己的信息，您可以自由地将这些信息存储在您的数据库中。

在本教程中，我们不打算在数据库中存储用户信息，我们只是探索一旦用户通过同意认证而信任我们，我们将从 Instagram API 中获得什么样的数据。

最终结果看起来会像这样

[![](../Images/98447dfb79e75d14a5c75770bdb44c68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1DPOGyZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmjg8cdv2qinqwtxjs4r.png)

主屏幕将只包括一个登录按钮，一旦用户使用他们的 Instagram 凭据登录，他们将看到上面的页面填充了他们的信息。

## 使用 Express 设置应用程序

这些是我们将要使用的唯一的软件包

*   [express](https://expressjs.com/) -我们将使用的 web 框架
*   [pug](https://github.com/pugjs/pug) -模板引擎
*   [express-session](https://github.com/konteck/express-sessions) - express 中间件创建会话
*   [passport](http://www.passportjs.org/) -认证中间件
*   [passport-instagram](https://github.com/jaredhanson/passport-instagram)--“使用 OAuth 2.0 API 进行 insta gram 认证的 Passport 策略。”
*   [axios](https://github.com/axios/axios) - HTTP 客户端

大家都来下载吧:

```
npm install --save express express-session passport passport-instagram axios pug 
```

Enter fullscreen mode Exit fullscreen mode

使用`--save`标志确保这些包被写入`package.json`文件。

让我们创建一个 express 应用程序的基本结构。在`server.js`中添加以下代码:

```
import express from 'express';
import session from 'express-session';
import passport from 'passport';
import Instagram from 'passport-instagram';
import axios from 'axios';
const app = express();
const port = process.env.PORT || 5656;

app.use(express.static(__dirname + '/public'));
app.set('view engine', 'pug')

app.get('/', (req,res) => {
  res.render('login')
})

app.listen(port, () => console.log(`http://localhost:${port}`)) 
```

Enter fullscreen mode Exit fullscreen mode

这是绝对的最小值，当应用程序运行时，在主页(`/` route)上呈现`views/login.pug`，其代码如下所示。

```
doctype html 
html
  head
    title=title
    link(rel='stylesheet', href='/style.css')
    meta(name='viewport' content='windth=device-width, initial-scale=1')
body 
  .wrap 
    ul.provider_login 
      li 
        a(href='/auth/instagram') Login with instagram 
```

Enter fullscreen mode Exit fullscreen mode

如果你是 express 的新手，我会推荐我的教程，教你如何用 express 建立一个基本的网站

### 草签护照

Passport 是一个认证中间件。我们必须将它作为中间件添加到我们的 express 应用程序中。

```
// express-session setup 
app.use(session({
  secret: 'sytr456-65tyrd-12wrt',
  resave: true, 
  saveUninitialized: true
}))

app.use(passport.initialize());
app.use(passport.session());

passport.serializeUser((user, done) => {
  done(null, user)
})
passport.deserializeUser((user, done) => {
  done(null, user)
}) 
```

Enter fullscreen mode Exit fullscreen mode

在第一个`use()`方法中，我们设置了快速会话。

接下来的两行我们初始化 passport。然后通过`serializeUser` passport 得到一个响应(我们称之为`user`)，如果认证成功的话。使用`done(null, user)`,我们将整个响应对象传递给应用程序会话。我们这样做是因为我们只是展示从 instagram 返回的数据。如果我们只是使用 passport 来验证用户，那么我们会选择将用户的 ID 传递给会话`done(null, user.id)`,我们会将其添加到数据库等等，但对我们来说，我们希望 Instagram 发回所有信息。

`deserializeUser`然后，简单地从会话中删除用户信息(当用户注销时)。

## 设置 Instagram 策略

有“480 多种策略”可供选择，因此每个策略都应该单独安装和设置，并作为中间件传递给 passport，这是合乎逻辑的。

我们已经安装了`passport-instagram`，所以让我们来设置它。

```
import Instagram from 'passport-instagram';
const InstagramStrategy = Instagram.Strategy; 
...
passport.use(new InstagramStrategy({
  clientID: "...",
  clientSecret: "....",
  callbackURL: "http://localhost:3000/auth/instagram/callback"
}, (accessToken, refreshToken, profile, done) => {
  done(null, profile)
})) 
```

Enter fullscreen mode Exit fullscreen mode

大致来说，当用户点击“用 Instagram 登录”时，上面的代码就会被触发。用户被定向到 Instagram，以确认他们希望允许我们访问，然后他们被重定向到`/auth/instagram/callback`。此外，一些数据随着批准的请求返回，`InstagramStrategy`将这些数据传递给 passport，passport 又将这些数据注入到会话中(正如我们已经介绍过的`passport.serializeUser((user, done) => { done(null, user) })`

#### 创建我们的开发者(应用)`clientID`和`clientSecret`

作为开发人员，确保您已登录 Instagram，然后导航至[开发人员](https://www.instagram.com/developer/clients/manage/)区域，点击“注册新客户”并填写表格。

[![](../Images/4a0127a84c8738cab52a829cfc1dc68c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o0m8fM9M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pzf6a0hac96vhj0loqw.png)

**绝对确保**网站 URL 与你的本地主机匹配，并且“重定向 URI”与我们在上面指定的`callbackURL`匹配。

完成注册后，你会看到你新创建的客户，你需要点击“管理”，你会看到“客户 ID”和“客户密码”，你需要复制并粘贴到上面。

## 配置路线

首页路径(`/`)是登录页面。`/auth/instagram`将尝试验证用户。`/auth/instagram/callback`是 Instagram 在完成认证后将自己重定向到的地方。`/users`是用户认证成功后的登陆页面。

```
app.get('/', (req, res) => {
  res.render('login')
})

app.get('/auth/instagram', passport.authenticate('instagram')) 
```

Enter fullscreen mode Exit fullscreen mode

对于主页，我们渲染一个`login.pug`文件。当在`/auth/instagram`上时，我们调用 passport 来尝试验证。此时，用户会被带到 Instagram 页面，并被询问是否要给我们访问权限。然后 Instagram 将它们重定向回我们的网站，地址是`/auth/instagram/callback` :

```
 app.get('/auth/instagram/callback', passport.authenticate('instagram', {
   successRedirect: '/users',
   failure: '/'
 })) 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，如果认证成功，我们将用户重定向到`/users` :

```
app.use('/users', (req,res, next) => {
  if(!req.user){
    res.redirect('/')
  }
  next()
})
app.get('/users', (req, res) => {
  res.json(req.user)
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了确保`/users`路径是私有的，未经认证的人无法访问，我们添加了一个简单的中间件，通过它我们可以检查用户(来自 Instagram 认证)是否存在，如果不存在，我们将重定向到主页/登录页面。否则，我们将在浏览器中呈现整个响应(**这有助于你看到你得到的一切**——我发现它在开发时很有帮助)

## 让我们把`/users`页面做得好看些

在这里，我们将开始做一些重构。当用户通过身份验证时，我们将整个响应存储在会话中(因此在`req.user` )

```
passport.use(new InstagramStrategy({
  ...
}, (accessToken, refreshToken, profile, done) => {
  done(null, profile)
})) 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不需要存储返回的所有内容。让我们储存我们需要的东西

```
passport.use(new InstagramStrategy({
  clientID: "****",
  clientSecret: "****",
  callbackURL: "http://localhost:3000/auth/instagram/callback"
}, (accessToken, refreshToken, profile, done) => {
    let user = {};
    user.name = profile.displayName;
    user.homePage = profile._json.data.website;
    user.image = profile._json.data.profile_picture;
    user.bio = profile._json.data.bio;
    user.media = `https://api.instagram.com/v1/users/${profile.id}/media/recent/?access_token=${accessToken}&count=8`

    done(null, user)  
})) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只是得到了用户的基本信息。此外，在`user.media`中，我们创建了 API 端点，稍后我们将使用它来访问用户的照片。注意，API 需要用户的 ID(我们可以通过`profile.id`访问)和用户的访问令牌(我们可以通过`accessToken`访问)。我还选择将条目数限制为 8 个。

最后，它是存储在应用程序会话中的`user`对象。

## 创建`/user`页面

现在，我们可以调用 Instagram API，取回 8 张图片，并将它们全部传递给`instagram.pug`模板

```
app.get('/users', (req, res) => {
  axios.get(req.user.media)
  .then(function (response) {
    const data = response.data.data;
    let user = req.user;
    user.images = data.map(img => img.images);
    res.render('instagram', user)  
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我选择将`views/instagram.pug`构造成这样

```
doctype html 
html
  head
    title=title
    link(rel='stylesheet', href='/style.css')
    meta(name='viewport' content='windth=device-width, initial-scale=1')
body 
  .wrap 
    img.cover(src=images[1].standard_resolution.url)
    .content
      h1=name
      a(href=homePage) website
      p=bio
      each image, i in images
        img.shots(src=image.thumbnail.url)  
      a(href='/logout') Logout 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样

我已经将所有的节点/JavaScript 代码添加到`server.js`中，这是为了避免分散注意力，抓住重点。但是你可以，也应该按照你觉得合适的方式来分割代码。考虑到更大的项目，分割代码的一种方法是在单独的文件中添加路线和策略。你可以检查一下 [github 库](https://github.com/aurelkurtula/Accessing-Instagram-API-with-PassportJS)来查看一种方法