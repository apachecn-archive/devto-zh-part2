# 准备部署！Vue + Flask，但是部署到哪里呢？

> 原文：<https://dev.to/danroc/ready-to-deploy-vue--flask-but-where-to-deploy-it-to-57o3>

大家好！

经过几周对我的应用程序的辛勤工作，我很想最终将它部署到生产服务器上。这东西是这样建造的:

*   后端: **Python Flask + Postgresql** 作为 API 端点
*   前端: **Vue.js** SPA，axios 调用我的 API 来填充数据

你会把这样的东西放在哪里？我在想， *Heroku* 还是*数字海洋*？还是没有？关于我应该注意什么有什么建议吗？

尽管静态站点的想法很有吸引力(使用 nuxt.js 或 VuePress)，但它对我来说并不适用，因为我有很多关系数据，用户可能会以不可预知的方式显示这些数据。此外，在 2.0 版上将会有一个用户帐户系统，所以我猜这将取消该网站的资格，对吗？

非常感谢！
最佳，
丹尼尔