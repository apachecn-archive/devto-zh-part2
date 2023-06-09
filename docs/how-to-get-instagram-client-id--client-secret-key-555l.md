# 如何获取 Instagram 客户端 ID 和客户端密钥

> 原文：<https://dev.to/franken/how-to-get-instagram-client-id--client-secret-key-555l>

[![](img/78835f1959ddcc1872f2282534d82772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_xBq3gc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/instagram-client-id.jpg)

**Instagram 客户端 ID** 和**客户端密钥**用于在 Prestashop 网站上集成 Instagram 社交登录模块，允许访问者使用 Instagram ID 访问网站。

许多客户报告说，由于不得不创建新帐户来买东西，他们感到很累，社交登录模块是一个很好的解决方案，可以提高 Prestashop 电子商务网站上提供的转化率，这将为商家提供丰富的机会。

我们还计划发布一个 Prestashop 社交登录模块(Fb，Twitter，Instagram...).这个模块叫什么？告诉我一个名字！

## Instagram 客户端 ID 和客户端密钥——它们是什么？

客户端 Id 是 Prestashop 模块的公共标识符，客户端密钥是只有模块和授权服务器知道的秘密。

没有它们，你的模块将无法与 Instagram API 交换数据。

[![Instagram Client Id](img/78835f1959ddcc1872f2282534d82772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_xBq3gc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/instagram-client-id.jpg)

## 如何获取 Instagram 客户端 ID 和客户端密钥

### 第一步——访问 Instagram 开发者网络

参观[https://www.instagram.com/developer/register](https://www.instagram.com/developer/register)

[![Instagram Developer Page](img/df640cf212d17e9e4628f6ade2451d64.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eFq9dTIZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/instagram-developer-page.jpg)

点击登录按钮登录 Instagram

[![Login InstaGram](img/50914c3ed4ce3ae247c5ffd9fb5ac8d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vO1ocCVv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/login-instagram.jpg)

作为 Instagram 用户登录，或注册一个新帐户，继续本 [Prestashop 教程](https://www.prestasoo.com/Blog/?utm_source=devto&utm_medium=article&utm_campaign=instagram)。这很容易。我保证每一步都一样。

### 第二步-开发者注册

在几秒钟内填写这些字段，注册成为开发人员:

[![Developer Signup](img/f69cf299816457f4322a367e774641c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iTA03GKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/instagram-developer-signup.jpg)

完成后，是时候构建您的第一个应用程序，将 Prestashop 社交登录模块连接到您的 Instagram API。

### 第三步-注册您的应用程序，获取客户端 Id 和客户端密钥

点击注册您的应用程序开始

[![Register Application](img/38c260f80efcf01ec507fb9e04160458.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D1l8oVe8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/register-application.jpg)

填写表格中要求填写的所有字段:

应用程序名称:应用程序的名称。应该是 Prestashop 社交登录。描述:写一份你的应用程序的简短描述。
网站网址:完整网址。例如:https://yoursite.com。你的网站应该有 SSL 证书，否则你的网站排名会很低。有效的重定向 URL:必须是 https://yoursite.com/modules/my-social-login-module/instagram

确认所有信息后，点击注册按钮查看最终结果。

[![Register New Client Id](img/b35eef18431d336da6dff18a755f0e72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i6x44R5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/register-new-client-id.jpg)

第四步-最终

搞定了！您已经获得了 **[Instagram 客户端 ID](https://www.prestasoo.com/blog/instagram-client-id.html?utm_source=devto&utm_medium=article&utm_campaign=instagram)** 和**客户端密钥**！在 [Prestashop 模块](https://www.prestasoo.com/prestashop-addons.html?utm_source=devto&utm_medium=article&utm_campaign=instagram)的管理面板中复制并粘贴客户端 ID 和客户端密钥。

[![Client Id and Client Secret](img/5ff782e39bb5c6b7cee95b5d5629562b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8NlKMb01--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.cimg/stories/client-id-client-secret-key.jpg)