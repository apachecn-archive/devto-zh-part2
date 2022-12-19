# 使用 Cloudflare 创建域重定向的分步指南

> 原文：<https://dev.to/hugo__df/the-step-by-step-guide-to-create-a-domain-redirect-with-cloudflare-48l6>

登录到您的仪表板。

1.  选择要重定向的域
2.  转到页面规则 [![](img/ccac69d38e5fa1f453a72a1341bed967.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3spz-ZaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378164299_image.png)

3.  点击【创建页面规则】 [![](img/2c31148a60f513ee8372ece7913b3216.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nW_WFwmm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378182704_image.png)

4.  输入`your.domain/*`(匹配所有流量)，URL 的流量将被重定向

5.  点击【添加设置】 [![](img/7da17909cb46da878555f48b984a6ad1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zb8wAcM_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378353766_image.png)

6.  选择【转发网址】 [![](img/52c694ad2b3c06c65aa5d33cf6c94c46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F2e02nN5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378376456_image.png)

7.  选择重定向的类型，我推荐 302，因为它们的缓存不那么激进(所以如果你想用那个域做重定向以外的事情，你可以) [![](img/1413e909b712c4a168ea0b71da016dfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--alIwSFxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378448011_image.png)

8.  输入您的转发网址 [![](img/10fb8f0dc98e869e19a9079d784caf19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hRbP9REa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378393120_image.png)

9.  点击 [![](img/ed6820c64a3980b8efb09f2e81ba1941.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8yjGNkWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_DC48416CB036B302B07CB3A97C1D5F782A909AB40C4D67DF37729B755DC42D54_1533378519046_image.png) 的【保存并部署】

完成了，您刚刚创建了一个从您的域到另一个域的重定向🙂

安德鲁·普雷布尔