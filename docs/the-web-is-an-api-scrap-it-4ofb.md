# 网络是一个 API(放弃它！)

> 原文：<https://dev.to/julbrs/the-web-is-an-api-scrap-it-4ofb>

我是如何使用 Python API BeautifulSoup 来增强网站的。

我目前住在蒙特利尔，那里有非常好的公共图书馆，可以借书和各种东西。每次你把一件东西带回家，都会有一个系统来管理它。它带有一个公共网站，可以帮助你检查你是否必须还书，或者你是否想延长借阅期。

重点是我有 3 个孩子，我妻子也有一个账户。是的，我们在系统上有 5 个帐户，当我必须检查每个帐户时，事情开始变得困难！

[![1*RHJgbEq5x58OfM_3KbkHOQ.png](../Images/ae2361c108b21dd4b1b85ae6ea3e288d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XghdEg_5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1578665674212/bqGFWTU4R.png)

*Nelligan 目录，最终用户图书馆系统*

目录系统被命名为 *Nelligan* ，但是在[网站](https://nelligan.ville.montreal.qc.ca/search)的源码上搜索，你会很容易地发现它是一个名为 [III Encore](https://www.iii.com/products/sierra-ils/encore-discovery/) 的商业解决方案。公共网站不允许*多账号*选择某个东西。

我的目标是能够:

*   快速检查我需要把书带回图书馆的日期，以避免罚款
*   能够延长贷款期限

所以让我们试着检查一个 API 是否存在，以便在它上面写点什么！过了几天，我什么也没看见。公司 [III](https://www.iii.com) 似乎提供 API，但没有提供公共接口的东西……我刚刚在这里发现一个 python 库正在为库的多个商业系统做这种工作；但是 III Encore / Web PAC Pro(貌似是同一款产品)的实现好像还没完成。

我已经决定在这个 GitHub 库上做一些工作，我发现花时间在 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) 上非常有用，那是一个网页抓取器(即你可以通过一个脚本在网上阅读你想要的东西！).结合[请求](http://docs.python-requests.org/en/master/) API，我拥有了制作我的项目所需的所有工具。我决定将 [Django](https://www.djangoproject.com/) 用于 web 部件，因为所有的东西都是用 Python 写的，而且因为我想学习它！

主要是游戏没那么难:

*   找到一种通过请求 API 登录的方法:

```
login = {'code': code, 'pin': pin}
r = requests.post(NELLIGAN_URL + '/patroninfo/?', data = login) 
```

Enter fullscreen mode Exit fullscreen mode

*   然后想办法在页面中读取我想要的数据:

```
# Grab loans (currently taken)
soup = BeautifulSoup(r.text, 'html.parser')
items = soup.select("tr.patFuncEntry")
for item in items:
    # do things on books 
```

Enter fullscreen mode Exit fullscreen mode

我的主服务文件是[这里是](https://github.com/bobman38/nelligan/blob/master/library/services.py)，它包含了所有对网站的调用。它允许我测试卡，获取实际贷款，延长一本书的贷款，请求书籍，查看与每张卡相关的罚款…

然后很容易在贷款到期日之前订购图书…

[![1*J0THuAHs40876n4gLLIO4w.png](../Images/9aa6d76f4f67e303a91427688ec69527.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bZ_XkRGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1578665723571/T1-aesJGk.png)

*在借出日期结束前从多张卡订购的书籍*

要了解更多有关该应用程序的信息，请点击以下链接:

*   GitHub 库:[https://github.com/bobman38/nelligan](https://github.com/bobman38/nelligan)
*   一个 Heroku dyno 上的应用(爱好一请稍候！):[http://nelligan.herokuapp.com/](http://nelligan.herokuapp.com/)

这个应用程序的下一个目标是什么？我真的想为这个[项目](https://github.com/BenjaminEHowe/library-api)做出贡献，为图书馆管理提供一个全球 API(不仅仅是一个商业应用！).然后把这个 API 集成到我的 web 应用中(不仅仅是蒙特利尔 Nelligan 系统焦点)。并且有一天尝试用不同的语言(比如 JS)为一个等价的 NodeJS 系统复制 API。