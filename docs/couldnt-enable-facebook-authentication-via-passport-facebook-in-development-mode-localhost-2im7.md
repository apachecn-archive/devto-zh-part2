# 无法通过 passport 启用 facebook 身份验证-facebook 处于开发模式(本地主机)

> 原文：<https://dev.to/vinhlee95/couldnt-enable-facebook-authentication-via-passport-facebook-in-development-mode-localhost-2im7>

大家好，
我正在开发一个使用脸书认证的 web 应用程序。我用的技术是服务器端的 passport-facebook 和 express。具体来说:

route.js

```
 app.get('/auth/facebook', 
      passport.authenticate('facebook', {scope: 'email'} ));

   // 
   app.get('/auth/facebook/callback',
      passport.authenticate('facebook', {
         successRedirect: '/',
         failureRedirect: '/login'
      })); 
```

Enter fullscreen mode Exit fullscreen mode

passport.js

```
const passport = require('passport');
const FacebookStrategy = require('passport-facebook').Strategy;
const keys = require('../config/keys');

passport.use(new FacebookStrategy({
   clientID: keys.facebookClientID,
   clientSecret: keys.facebookClientSecret,
   callbackURL: '/auth/facebook/callback',
   }, (accessToken, refreshToken, profile, done) => {
      console.log(accessToken, profile);
   }
)); 
```

Enter fullscreen mode Exit fullscreen mode

我在开发服务器 localhost:3000 上运行应用程序，当转到 route:localhost:3000/auth/Facebook 时，弹出一条错误消息:

> "不安全登录被阻止:你无法从不安全的页面获取访问令牌或登录此应用。请尝试将页面重新加载为 https://"

我试图谷歌和 StackOverFlow 这个，但找不到答案。非常感谢你的帮助！
谢谢大家&周末愉快！