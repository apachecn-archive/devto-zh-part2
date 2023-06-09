# 使用 Siri 自动触发 Netlify 的手动部署

> 原文：<https://dev.to/chetan_raj/auto-trigger-manual-deployment-of-netlify-with-siri-5272>

苹果公司于 9 月 17 日发布了最新的 iOS 版本 iPhone 版 iOS 12

1.  此外，苹果发布了一个新的应用程序 [Siri 快捷键](https://itunes.apple.com/us/app/workflow/id915249334?mt=8&pt=11l8808&ign-mpt=uo=4)，我们将使用它来触发手动部署。

[![](img/0f0354d512790cfbfb5ea90d555b9c15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8OxbtFNn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Awx6Tf9Y8R-nzU9WQHARLFg.jpeg) 
<small class="figcaption_hack">【照片来源】
[【https://un flash . com/photos/04 tym 24 wi 2c](https://unsplash.com/photos/04TYM24Wi2c)</small>

首先，在您的 Netlify 项目中创建一个构建钩子 URL

1.  转到—[https://app.netlify.com/sites/](https://app.netlify.com/sites/)/设置/部署
2.  点击**添加构建钩子**按钮，进入详细信息
3.  生成**构建挂钩 url**
4.  复制**构建钩子 URL**-【https://api.netlify.com/build_hooks/ T2】

* * *

启动快捷方式应用

步骤 1:创建快捷方式

[![](img/887ba46e5971d76543958d47e6260f0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JZFLctVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AOXsXU-S8QtEUe1RPB8VBDA.png)

第二步:点击右上角的设置图标，输入名称，然后
点击完成。

[![](img/0e006cadd9b1b5a401b5558d002f6857.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZH3hqfsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AN7vN55x4ZE1vAaYjpbplrw.png)

第三步:搜索 **URL** ，然后在文本中输入你的应用
的 Netlify build hook URL，如下所示。

[![](img/93bf9f7c348b970cdf200a3a996675cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aOI7vhlC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AgvElSG5dtei7f091L_Wcug.png)

步骤 4:搜索**获取 URL** 的内容，将方法类型改为* * POST
* *如截图所示。

[![](img/55a2c2b862434c153711fa645dc8c7ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1yb1_pvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AoljssYZ9KvLHOVTb0pcZiA.png)

询问 Siri

> 触发网络部署