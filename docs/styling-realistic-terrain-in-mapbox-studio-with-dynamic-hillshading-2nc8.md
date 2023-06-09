# 在 Mapbox Studio 中使用动态山体阴影设计真实地形

> 原文：<https://dev.to/eqmapbox/styling-realistic-terrain-in-mapbox-studio-with-dynamic-hillshading-2nc8>

这篇文章需要根据最近的工作室发布进行更新。抓紧了！

最近，我们发布了一个名为 [Mapbox Terrain RGB](https://blog.mapbox.com/new-in-studio-dynamic-hillshading-2027c77781d8) 的新的光栅数字高程模型(DEM)源，用于设计地形地图。在我们的制图编辑器 Mapbox Studio 中设计任何地图时，这些数据可以通过[添加 tileset 作为新图层](https://www.mapbox.com/help/studio-manual-styles/#hillshade-layer)获得:

[![](img/cf933b81f0a85859a99ec26b6352afaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3l5dtcHh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523914880773_image.png)

本指南将特别使用 [Tristen 的浮雕风格](https://www.mapbox.com/studio/styles/add-style/mapbox/cjfshapp373un2rsyqe11pbvc/?utm_source=multi-step-map&utm_medium=email&utm_content=add-relief-style&utm_campaign=multi-step-map) (←你可以从这个链接添加到你的帐户)作为画布来构建一个自定义风格的地形图。让我们开始吧！

## 调整`hillshading`图层的颜色&强度

花点时间熟悉一下工作室界面。在你的左边是[图层列表](https://www.mapbox.com/help/studio-manual-styles/#layers-list)，它详细描述了组成你的地图的数据类型。这种样式使用了少量常见的土地利用类型，非常适合让晕渲地貌图真正脱颖而出。

[![](img/7cd649e42bd530857a92197a5631a08a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVntEhlw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523814581722_image.png)

让我们首先选择一种配色方案来制作您自己的地图:

*   从图层列表中选择您的`hillshading`图层(如上所示)
*   点击每个面板- `shadow color`、`highlight color`和`accent color` -尝试你选择的互补色

[![](img/937d15258ece07c669876ebe9e20b1bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--To5HsOOH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523917639324_image.png)

注意，从设计的角度来看，当`shadow`和`highlight`属性的颜色不透明度被调整(使用你的颜色窗口中的右开关)时，`accent color`工作得很好

[![](img/d87a6364d3022bfef7ff6b974c4665b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b8XkuR7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523973279989_color-opacity.gif)

就我的风格而言，我选择了喜庆的亮粉色/紫色/蓝色主题:

[![](img/d7e4ff6ad924e5997d55e0fe2e70cecc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2vO04Wc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523814905353_image.png)

另一个样式选项是调整图层使用的所有颜色的`Intensity`——这张截图的亮度设置为 0.65 以获得更柔和的外观，而下面的其他设置为 1 -

[![](img/1229c8507a958d3069461d2004d4a54d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1h64lHUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523973209411_image.png)

当你设计时，你可能会发现使用搜索框查找山区很有帮助——试试尼泊尔的加德满都或阿拉斯加的德纳里国家公园:

[![](img/ab536679eef4c4e374a3b268a13c9f12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1R2DN4Z1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523973613612_image.png)

## 为所有 12 层继续造型`color`和`intensity`

点击每一层，选择适合你的主题的颜色，如上所述。我的调色板🎨：

[![](img/1b5fafcfc65abdbae46336830c7bea8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hYjltMeb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523915086296_image.png)

[![](img/24ab225fda12d4e0aa1262e215ef0fef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FRaZ3xQD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523815910183_image.png)

关于 Tristen 的原始地图设计有一个有趣的注意事项——他使用了工作室手册中描述的造型技术`set value by zoom`，当以更高的缩放级别显示时，在水和山体阴影之间创建了更好的对比。自己放大地图看水从青绿色变淡→淡蓝色:

[![](img/13c35d85821567a3a07293db9a5b44c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7xX_HNmA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523917888057_image.png)

## 造型`illumination direction`

现在你已经有了基本的东西，我们可以试着模仿莫莉在她的博客文章[中模拟太阳如何穿过地形](https://blog.mapbox.com/realistic-terrain-with-custom-styling-ce1fe98518ab)。对于你的`hillshading`层，点击进入`illumination direction`,用滑块玩一玩

[![](img/a7347d49b4624c4288b064b892a63e99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8lsmqqFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523974592771_high%2Bres%2Bterrain%2Bstyling.gif)

值得注意的是，Molly 在她的文章中用 JavaScript 库 Mapbox GL JS 实现了这个效果[。查看](https://blog.mapbox.com/realistic-terrain-with-custom-styling-ce1fe98518ab)[山体阴影代码示例](https://www.mapbox.com/mapbox-gl-js/example/hillshade/)和[山体阴影样式规范](https://www.mapbox.com/mapbox-gl-js/style-spec/#layers-hillshade)，使用 JavaScript 在运行时更改这些属性。

## 卡住了？我们在这里支持你！

太平洋标准时间本周四下午 1-2 点，我们将在 Twitter 上为#mappyhour 回答问题。使用该标签发送任何问题，我们将与您一起实时解决这些问题。但是不要担心，我们很乐意在你遇到难以置信的困难时随时帮助你- [联系我们](https://www.mapbox.com/contact/support/#products/studio)！在应用程序中，您可以通过屏幕右侧的`Help`面板联系我们

[![](img/0d7b40431ff72db39f9bb9cdb29ee1aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y2mLP8DV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_8AED25FC82CB6C241545BF740F064C73E1B15B16CDB163B4F93AB51FC754ECEA_1523813254622_image.png)

对下一期指南有任何问题或要求，请联系我！