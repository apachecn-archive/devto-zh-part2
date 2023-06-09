# Django 电子商务指南(奖金:一个 Wagtail 商店教程)

> 原文：<https://dev.to/couellet/guide-to-django-e-commerce-bonus-a-wagtail-shop-tutorial-49ej>

[![Guide to Django E-Commerce (Bonus: a Wagtail Shop Tutorial)](img/22e1768237392e533e777bb699d2f3af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9bBqDfsX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203682/wagtaildjangosnipcart-1.png)

" D-J-A-N-G-o . D 不发音."

伙计，我喜欢这句台词。太坏了。

但是昆汀·塔伦蒂诺杰作中的同名角色并不是镇上唯一的坏蛋姜戈。

流行的同名 Python 框架也是如此。

今天，我将离开 [JavaScript](https://snipcart.com/blog/react-seo-nextjs-tutorial) [框架](https://snipcart.com/blog/vuejs-ecommerce-headless-buttercms)的领域，快速进入 **Django 电子商务。**

[![django](img/7847e6736252eec8664cc732ad1259e3.png)T2】](https://i.giphy.com/media/m7xbffjvR3ZD2/giphy.gif)

在这篇文章中，我将回答你在开始一个新的电子商务项目时可能会有的合理问题，比如:

> Python 是我项目的合适语言吗？Django 是正确的框架吗？我应该使用哪些工具或插件？

然后，我将通过一步一步的 **Wagtail CMS** 教程向你展示我们自制的基于 Django 的电子商务成功秘诀:

*   创建一个新的 Wagtail 网站。
*   添加 Snipcart 配置设置。
*   生成数据库迁移。
*   为你的 Django 商店创造新产品。
*   制作电子商务模板。

让我们从基础开始。

## 蟒蛇的状态

选择 Django 作为框架的一个主要原因是它的 [**Python**]((https://www.python.org/)) 基础。

Python 是一种通用的动态编程语言，由前谷歌员工吉多·范·罗苏姆在 80 年代后期开发。作为 Monthy Python 的粉丝，他用这个名字的一半来命名他的编程项目。

[![monty-python](img/cce39adf03a93f087486bbffb9d698e4.png)T2】](https://i.giphy.com/media/Tim0q7zolF3fa/giphy.gif)

怎么可能不是范呢？

不过，他不是在开玩笑。说 Python 变得“流行”是轻描淡写。

今天，全世界成千上万的开发人员都在使用它。正如 StackOverflow 所说:

> 术语“发展最快”可能很难精确定义，但是我们认为 **Python 有充分的理由成为发展最快的主流编程语言。**

几个原因解释了蟒蛇的爱:

*   它的语法可读性很棒。
*   对于新人来说，它有一个快速的学习曲线。
*   它拥有一个持久而稳固的图书馆生态系统 &社区
*   它现在是[数据科学](https://jakevdp.github.io/PythonDataScienceHandbook/) & [机器学习](https://machinelearningmastery.com/machine-learning-in-python-step-by-step/)的标准语言。
*   它为优秀的开发工具提供了动力，比如一个整洁的静态博客生成器 [Pelican](https://snipcart.com/blog/pelican-blog-tutorial-search-comments) 。
*   Reddit 是用 Python 写的。；)

## Django 框架怎么样？

[**Django**](https://www.djangoproject.com/) 是开源的，**高级 Python web 框架**。它对可重用组件的强调使得开发人员可以更快地在 Python 上构建 web 应用程序。它把自己描述成一个为有期限的完美主义者准备的网络框架。

[![django-framework](img/b378ec5e87441e0c1914beeaa949ba71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZeWnGZjX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203683/screen-shot-2018-07-04-at-34010-pm.png)

现在由 Django 软件基金会维护，它最初是由两位杰出的劳伦斯日报世界开发者编写的。哦，Python 的名字来源于喜剧偶像，Django 的名字来源于一个多才多艺的吉他传奇人物:[坦哥·雷恩哈特](https://www.youtube.com/watch?v=aZ308aOOX04)！

作为一个全栈框架，它几乎盖过了任何其他工具。它快速、满载、安全、可扩展且功能多样。所有你可能想应用到你的电子商务设置的特征！

## 为什么要用 Django 做电商？

虽然你可以用 Django 做很多事情，但让我们把重点放在它给电子商务带来了什么，以及可以用来建立网上商店的不同工具上。

首先，如果你正在寻找合适的框架来建立一个商店，这里有一些 Django 的特性可以考虑。

→ **可扩展性**

Django 非常适合电子商务初创公司，因为它非常适合小型网站，而且可以随着业务增长完美扩展。你可以依靠 Django 同时处理成百上千的访问者。它由独立的组件组成，您可以根据自己的需要随时拔下或更换。

→ **安全**

有了电子商务，你想确保商家和客户在你的购物体验中感到安全。Django 防止了很多常见的安全错误，这些错误经常会削弱传统的 PHP CMSs。例如，Django 通过动态生成网页来隐藏你的站点的源代码，避免在网上被直接看到。

→ **功能丰富的**

与大多数框架相比，Django 提供了更多开箱即用的特性。它可以让你立刻构建一个应用程序。非常适合使用用户验证、内容管理或 RSS 提要等功能来支持您的在线商店。如果似乎缺少了什么，您可以依靠 Django 的社区和插件生态系统来扩展您的应用程序！

→ **SEO 友好型**

搜索引擎优化是最重要的任何在线业务。虽然其他框架不能很好地与搜索引擎兼容(主要是 JavaScript 框架，如 [Vue](https://snipcart.com/blog/vuejs-tutorial-seo-example) 或 [React](https://snipcart.com/blog/react-seo-nextjs-tutorial) ，但至少 Django 提倡 SEO 的最佳实践。人类可读的网址和网站地图功能肯定会取悦任何营销团队。

哦还有，**快了**。这对客户体验和 SEO 都有好处。

→ **可靠**

它已经被大众测试了一段时间，并且它周围的社区也广泛支持它。它由活跃的开发者不断更新；也许你会发现自己也在为 T1 做贡献。；)

阅读这篇文章的其余部分，找到技术教程[这里]。([https://snip cart . com/blog/django-ecommerce-tutorial-wagtail-CMS](https://snipcart.com/blog/django-ecommerce-tutorial-wagtail-cms))