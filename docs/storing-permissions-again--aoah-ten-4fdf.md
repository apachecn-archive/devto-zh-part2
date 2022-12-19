# 存储权限(再次)~ AoaH 十

> 原文：<https://dev.to/link2twenty/storing-permissions-again--aoah-ten-4fdf>

# (再次)在 SQLite 数据库中存储权限

## 开场

上周，我看了使用 SQLite 作为数据库，众所周知，我真的不知道我在做什么，这里有一个链接到我以前的帖子。我从 [@buinauskas](https://dev.to/buinauskas) 和 [@tiguchi](https://dev.to/tiguchi) ，还有 [@avalander](https://dev.to/avalander) 那里得到了一些关于安全的有用评论，理所当然地质疑我对一个类的使用，所以我也看了一下。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ ignis-pwa ](https://github.com/ignis-pwa) / [权限 _ 帮助者](https://github.com/ignis-pwa/permissions_helper)

### 创建和修改用于管理权限的 SQLite 文件

<article class="markdown-body entry-content container-lg" itemprop="text">

# 权限 _ 帮助者

创建和修改用于管理权限的 SQLite 文件

</article>

[View on GitHub](https://github.com/ignis-pwa/permissions_helper)

## 有哪些安全问题？

我已经准备好的代码没有问题，但是我的编码风格有问题。我使用模板自由主义者将字符串放入查询中，这意味着一个精明的用户可以操纵他们的输入来获得他们想要的结果。

例如，如果我在他们的 cookies 中检查一个字符串，看看他们是否登录，它可能看起来像这样。

```
function cookieLogin(session_id) {
  user_id = sql.get(`SELECT user_id FROM users_sessions WHERE session_id= "${session_id}"`);
  return user_id || false
} 
```

哪家会这么叫

```
// normal execution
cookieLogin('$2b$09$oGdnXBsnb8QPCJk/VY1JKuxG/QW66K8RB7n01kBhJsbB45nvIK0pK')
// SELECT user_id FROM users_sessions WHERE session_id="$2b$09$oGdnXBsnb8QPCJk/VY1JKuxG/QW66K8RB7n01kBhJsbB45nvIK0pK"

// problematicexecution
cookieLogin('foo" OR user_id="1')
// SELECT user_id FROM users_sessions WHERE session_id="foo" OR user_id="1" 
```

在有问题的调用`cookieLogin`中，我们总是会得到 user_id 1，不需要密码。

我被告知要看“准备好的陈述”，这是内置在我正在使用的`SQLite`包中的。SQL 注入现在只会返回一个错误！

## 你为什么使用一个类？

我认为这是应该做的事情，Avalander 向我展示了他们的一些项目，在这些项目中，他们有函数，并将数据库引用传递给函数。这对我有意义，所以我不再使用类，至少在这个项目中没有。

现在我的文件末尾有一个导出块，这正常吗？如果有更好的方法，请随时告诉我。

```
module.exports.actionLogin = actionLogin;
module.exports.addUser = addUser;
module.exports.checkPassword = checkPassword;
module.exports.setup = setup;
module.exports.updatePassword = updatePassword;
module.exports.userLogin = userLogin; 
```

## 现在效果如何？

它的工作原理基本上和以前一样。现在，我不必实例化一个新类，只需运行一个`setup`函数，告诉它权限数据库在哪里。

```
const ph = require('./permissions_helper');

(async _ => {
  const sql = await ph.setup('perm.db');
  ph.userLogin(sql, 'admin', 'default', 1).then(key => {
    console.log(`Welcome admin you secure key is ${key}`);
  }).catch(err => {
    console.log(err);
  })
})() 
```

像这样做`async`感觉有点像黑客，但我不喜欢整个应用程序都有一个`.then(sql=>{})`，也许有更好的方法来做到这一点？

## 帖子结束

希望代码现在更好一点，将来不太可能给我带来问题，如果你看到任何问题，请随时留下评论，让我知道我真的很感激。

感谢您的阅读！

🦄❤🦄🦄❤