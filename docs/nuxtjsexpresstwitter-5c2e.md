# Nuxt.js+Expressで簡単にTwitter 認証

> 原文：<https://dev.to/dala00/nuxtjsexpresstwitter-5c2e>

在 Nuxt.js 和 Express 中创建 APP 应用程序时，尝试使用 Twitter 身份验证登录。 使用名为 Passport 的库，Node.js 上的 Twitter 身份验证非常简单。 虽然没有详细验证，但是在服务器端渲染( SSR )的情况下也应该没有问题。

我以这附近做的项目为基础进行尝试。

[用 Nuxt.js+Express 总之简单地 ORM](https://crieit.net/posts/Nuxt-js-Express-ORM)

*   Nuxt.js v1.4.2
*   护照-推特 1.0.4 版

## 安装

不管怎么样，先安装一下。 Passport 主机和推特的扩展。

基本上和官方的手册一样，如果不太清楚或者版本不同的话，请参考那里。

[文档:推特](http://www.passportjs.org/docs/twitter/)

```
yarn add passport passport-twitter 
```

Enter fullscreen mode Exit fullscreen mode

## Initial setting

### Passportの設定

首先，以 plugins/passport.js 的形式创建初始化脚本。 请将`TWITTER_CONSUMER_KEY`等指定为环境变量。 (使用 dotenv 等很方便)

以下内容也包含了在 Sequelize 中作为用户数据保存时的样本。

```
var passport = require("passport"),
  TwitterStrategy = require("passport-twitter").Strategy;

module.exports = function(app) {
  const models = app.get("models");
  const User = models.User;

  passport.use(
    new TwitterStrategy(
      {
        consumerKey: process.env.TWITTER_CONSUMER_KEY,
        consumerSecret: process.env.TWITTER_CONSUMER_SECRET,
        callbackURL: `${process.env.APP_URL}/auth/twitter/callback`,
        includeEmail: true // メールアドレスが必要な場合
      },
      async function(token, tokenSecret, profile, done) {
        let user = await User.findOne({
          where: { twitter_id: profile.id }
        });

        if (!user) {
          user = User.build({
            unique_id: profile.username,
            name: profile.displayName,
            email: profile.emails[0].value,
            location: profile._json.location,
            bio: profile._json.description,
            url: profile._json.url,
            image: profile.photos[0].value,
            twitter_id: profile.id,
            twitter_token: token,
            twitter_secret: tokenSecret
          });
          await user.save();
        }

        done(null, user.get({ plain: true }));
      }
    )
  );

  passport.serializeUser(function(user, done) {
    done(null, user);
  });

  passport.deserializeUser(function(user, done) {
    done(null, user);
  });

  return passport;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 认证用路由

创建用于身份验证的路由。 这次以 routes/auth.js 的形式划分了文件。 因为在动作中直接指定 Passport 的处理，所以如下所示，将整个路由设定函数化并读取。

```
const { Router } = require("express");

module.exports = function(app, passport) {
  const router = Router();

  router.get("/twitter", passport.authenticate("twitter"));

  router.get(
    "/twitter/callback",
    passport.authenticate("twitter", {
      successRedirect: "/",
      failureRedirect: "/"
    })
  );

  app.use("/auth", router);
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 嵌入到 APP 演示中

在 server/index.js 中读取它们并将其合并到 APP 应用程序中。

```
const passport = require("../plugins/passport")(app);

app.use(
  session({
    secret: "your secret"
  })
);
app.use(passport.initialize());
app.use(passport.session());

require("../routes/auth")(app, passport); 
```

Enter fullscreen mode Exit fullscreen mode

基本部分到此结束。 接下来是为了实际运转而进行的处理。

## 使之与 Nuxt.js 联合

因为 Passport 登录只是保存在 Express 端的会话中，所以也将其传递给 Nuxt.js 端。 使用 Store 使其可以从 Nuxt.js 上的任何位置访问，以影响整个 APP 应用程序。 在 Nuxt.js 的情况下，只需如下所示适当添加一个文件就可以简单地利用 Store。

store/index.js

```
export const state = () => ({
  authUser: null
});

export const mutations = {
  setUser(state, authUser) {
    state.authUser = authUser;
  }
};

export const actions = {
  nuxtServerInit({ commit }, { req }) {
    if (req.session.passport && req.session.passport.user) {
      commit("setUser", req.session.passport.user);
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

Store 的情况下，我觉得经常使用`SET_USER`而不是`setUser`。

之后就可以判断是否已经像下面这样以宽松的感觉适当登录了。

```
 <div v-if="$store.state.authUser">
      <img :src="$store.state.authUser.image">
    </div>
    <span v-if="!$store.state.authUser">
      <a href="/auth/twitter">ログイン</a>
    </span> 
```

Enter fullscreen mode Exit fullscreen mode

在脚本方面也可以像`this.$store.state.authUser`一样使用。

## 总结

虽然可能也有一部分写法给人感觉很粗糙，但总之非常简单地登录了 Twitter，并与 Nuxt.js 进行了合作。 如果还有动作异常的地方，我会随时追记。

其他谷歌账户的登录等也写在了官方的文件里。