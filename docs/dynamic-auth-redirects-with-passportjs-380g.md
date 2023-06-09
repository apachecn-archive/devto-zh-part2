# 使用 PassportJS 的动态身份验证重定向

> 原文：<https://dev.to/dangolant/dynamic-auth-redirects-with-passportjs-380g>

如果你花了很多时间编程，你可能已经与认证。如果您正在使用 Node，这很可能意味着您已经使用了 Passport。Passport 是一个非常棒的工具，它节省了数百万——如果不是数十亿——开发人员的时间，而且它拥有一个强大的插件生态系统，几乎可以用于你能想象到的任何提供商或后端。也就是说，作为一个高度可定制的库，针对利基用例的文档和社区答案不一定容易获得。当我遇到从认证循环的一端到另一端获取数据的需求时，我发现很难找到关于如何做到这一点的文档。

我正在做一个项目，这个项目我已经断断续续做了一年了。当我[第一次将它提供给一小组测试人员](https://dev.to/dangolant/a-call-for-testers-if-you-hoard-tabs-or-want-to-manage-your-reading-list-better-i-need-your-help-2fao)时，最常见的请求——非常令我懊恼——是添加更多的认证选项。我对受访者经常询问这个功能并不感到惊讶，但我对我快速推出的谷歌网络认证会足够好抱有一线希望，因为我有意识地走了一些捷径，将所有认证委托给谷歌，希望避免不得不处理它。不过，鉴于这是第一个需要修改的地方，我开始了漫长而缓慢的修改我的 auth 的旅程。

最终，我发现需要通过 auth 循环将数据传递回我的服务器。在我的用例中，我想让调用页面影响用户在成功授权应用程序后被重定向到哪里。我想到的解决方案是将目的地 URI 作为身份验证请求的查询参数传递。

我在如何实现它的问题上挣扎了很长时间，这比我愿意承认的时间要长得多，主要是因为缺少关于如何将数据传递回我的回调路径的很好的文档。大多数答案指向了`passReqToCallback`选项，但这最终被转移了注意力。我一直在努力解决这个问题，直到我偶然发现 Github 用户 [itajajaja](https://github.com/itajaja) 的[这个答案](https://github.com/jaredhanson/passport-oauth2/issues/96)，它详细解释了为什么我之前没有使用状态参数。

首先，您需要像往常一样设置您的 Passport 配置，在本例中，我们将使用`passport-github`。

```
 const GitHubStrategy = require('passport-github').Strategy;
    const express = require('express');
    const User = require('PATH/TO/USER/MODEL');
    const app = express();

    passport.use(new GitHubStrategy({
        clientID: GITHUB_CLIENT_ID,
        clientSecret: GITHUB_CLIENT_SECRET,
        callbackURL: "http://process.env.HOST:4000/auth/github/callback"
      },
      function(accessToken, refreshToken, profile, cb) {
        User.findOrCreate({ githubId: profile.id }, function (err, user) {
          return cb(err, user);
        });
      }
    ));

    // Aaaand wherever you define your router instance

    app.get('/auth/github',
      passport.authenticate('github'));

    app.get('/auth/github/callback', 
      passport.authenticate('github', { failureRedirect: '/login' }),
      function(req, res) {
        // Successful authentication, redirect home.
            res.redirect('/');
    });

    app.listen(4000); 
```

到目前为止，我们已经有了一个 Express 实例，当用户向`host:4000/auth/github`发送 GET 时，它向 Github 发送一个认证请求，并且我们已经将`passport`配置为在通过验证函数运行请求之后，将请求的响应“返回”到配置的回调路由。

不幸的是，默认设置留给我们一个相当静态的重定向方案。如果我想根据用户的某些属性或者根据请求路径重定向到一个路径，我可以*或者*在 switch 语句中设置一些 cases。然而，随着调用路由和提供者数量的增加，这种方法变得不可持续，特别是因为它很大程度上依赖于修改请求本身外部的状态。

幸运的是，`passport`为我们提供了一个`state`参数，作为通过 auth 循环传输数据的重要媒介。我们可以这样更新我们的`/auth`路线来使用它:

<figure>

```
 app.get(`/auth`, (req, res, next) => {
        const { returnTo } = req.query
        const state = returnTo
            ? Buffer.from(JSON.stringify({ returnTo })).toString('base64') : undefined
        const authenticator = passport.authenticate('github', { scope: [], state })
        authenticator(req, res, next)
    })

    app.get(
        `/auth/callback`,
        passport.authenticate('github', { failureRedirect: '/login' }),
        (req, res) => {
            try {
                const { state } = req.query
                const { returnTo } = JSON.parse(Buffer.from(state, 'base64').toString())
                if (typeof returnTo === 'string' && returnTo.startsWith('/')) {
                    return res.redirect(returnTo)
                }
            } catch {
                // just redirect normally below
            }
            res.redirect('/')
        },
    ) 
```

<figcaption>Props to @itajajaja for this code</figcaption>

</figure>

上面，我们从请求查询中提取了一个名为`returnTo`的参数，并在将其附加到 auth 请求的选项之前对其进行了 Base64 编码。当请求返回时，我们从返回请求的参数中提取`state`，然后解码并从中提取`returnTo`值。此时，我们验证`returnTo`的值并重定向到预期的目的地。

很简单，对吧？现在，你可以轻松地做更多的事情。比如在我的 app 里，我也通过状态传递附加参数:

```
const authenticate = (options) => {
  return (req, res, next) => {
    const { redir, hash } = req.query;
    const state = redir || hash 
? new Buffer(JSON.stringify({ redir, hash })).toString('base64') : undefined;
    const authenticator = passport.authenticate(options.provider, {
      state,
      // etc
    });
    authenticator(req, res, next);
  };
};

const callback = (provider, failureRedirect) => [
  passport.authenticate(provider, { failureRedirect: failureRedirect || defaultFailureRedirect }),
  async (req, res) => {
    if (req.isAuthenticated()) {
      const { state } = req.query;
      const { redir, hash } = JSON.parse(new Buffer(state, 'base64').toString());
      const user = (await User.findByID(req.user.id))[0];
      if (typeof returnTo === 'string' && returnTo.startsWith('/')) {
        if (hash) {
          User.set(hash)
        }
        return res.redirect(returnTo)
      }
    }
  }
] 
```

如上所述，如果我们在原始请求中传递参数`hash`,我们就能够在将用户重定向回目的地之前用我们传递的数据更新用户。哒哒！这样我们就可以很容易地跟踪用户上次登录时来自哪里，统一登录和注册路径，同时适当地重定向，等等。

您能想到通过 auth 循环传递数据的其他方法吗？请在评论中告诉我！如果你喜欢这篇文章，欢迎在 [dev.to](http://dev.to) 或 [Twitter](https://www.twitter.com/dangolant) 上关注我。