# 存储权限~ AoaH 九

> 原文：<https://dev.to/link2twenty/storing-permissions--aoah-nine-130f>

# 在 SQLite 数据库中存储权限

## 开场

嗨，这是正在进行的系列的第九部分，我正在学习编码，目标是制作一个 PWA 来帮助管理活动目录，我觉得，我们正在取得很大的进展。正如我说的这是第九部分，现在有圆点，你可能已经注意到了，在帖子的上面和下面，让你浏览我以前的帖子。或者这里有一个[索引页面](https://dev.to/link2twenty/adventures-of-a-hobbyist--index-4oj7)。

## 这是什么周项目？

你们中的一些人可能知道，我想要一个配置文件来存储不能轻易存储在远程数据库中的细节，比如数据库的位置。这周我一直在看 SQLite 而不是一个配置文件。SQLite 是一个 Lite 数据库，具有类似 SQL 的简单语法，存储为一个文件，这意味着它不需要服务来运行数据库。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ ignis-pwa ](https://github.com/ignis-pwa) / [权限 _ 帮助者](https://github.com/ignis-pwa/permissions_helper)

### 创建和修改用于管理权限的 SQLite 文件

<article class="markdown-body entry-content container-lg" itemprop="text">

# 权限 _ 帮助者

创建和修改用于管理权限的 SQLite 文件

</article>

[View on GitHub](https://github.com/ignis-pwa/permissions_helper)

我已经链接了上面的 GitHub 链接，这周我试着对我的代码做了一些更好的评论，如果仍然难以理解请告诉我，并给我一些如何整理我的风格的建议。

## 它是如何工作的？

它是一个助手类，叫做 Permissions，其思想是它绑定或创建数据库，然后使用一系列函数，可以查询该数据库。当然，密码是加密的。为此我使用了 bcrypt。

功能的一个例子是:

```
const ph = new Permissions();
ph.checkPassword('admin', 'default').then((match) => {
  console.log(match);
}).catch(err => {
  console.log(err)
}) 
```

这将返回`true`或`false`,取决于管理员的密码是否是“默认的”。`checkPassword()`函数返回这个承诺:

```
/**  
 * Checks password against database version.
 * @param {string} username The users username
 * @param {string} password The users password
 * @return {boolean} Passwords match
*/
checkPassword(username, password) {
  return new Promise(async (res, rej) => {
    if (!this.sql) await this._init();
    const dbPassword = await this.sql.get(`SELECT user_password FROM users WHERE username = "${username}"`);
    res(this.bcrypt.compare(password, dbPassword ? dbPassword.user_password : ""));
  })
} 
```

## 为什么这个标着‘help’？

如你所见，我一直在使用承诺，包括`await`和`.then`，但是构造函数不能是`async`，这就是为什么我做了一个`_init()`函数。我的问题是，当一个类需要等待才能被使用时，即使它很乱也可以使用`.then`吗？或者有没有我不知道的更好的方法？

我解决这个问题的方法，感觉很简单，就是让每个函数检查 init 是否已经完成，如果还没有完成，就执行并等待它完成。

## 注销

代码在 GitHub 上，请随时过来告诉我我做错了什么，或者你认为我可以做得更好。如果你喜欢，你也可以在这里留下评论，更多的评论和心形/独角兽意味着更多的人阅读帖子，我得到更多的投入，所以我非常感谢你们这样做。

非常感谢你读到这里，并忍受我的漫谈。

🦄❤🦄🦄❤