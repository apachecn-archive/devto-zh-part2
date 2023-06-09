# 在 Mapbox Studio 中挤出 3D 建筑物

> 原文：<https://dev.to/mapbox/extruding-3d-buildings-in-mapbox-studio-jg1>

*目前过时的有[最近工作室重新发布的](https://blog.mapbox.com/studio-expressions-design-81012e2dab55)，前往[https://www.mapbox.com/help/add-3d-buildings-studio/](https://www.mapbox.com/help/add-3d-buildings-studio/)进行更新！*

我们进入了 Mapbox 的第三个挑战[地图疯狂](https://www.mapbox.com/map-madness/)，我想交叉发布我们在 20 分钟或更短时间内将 3D 建筑添加到自定义地图样式的演练。

## 1。创建您的地图

如果您还没有在 [Cartogram](https://www.mapbox.com/cartogram/) 中制作地图，请拖放您喜欢的球队的照片来创建自定义地图样式。系统会提示您创建新帐户或登录。

[![](img/d6a3d914be6c9ed7e5e073340bbed32f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KrHJkxNJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521746687640_image.png)

注意横幅`Saved style!`指示您的新地图样式已保存到您的 Mapbox 帐户-

[![](img/9b64ebaf51543d707998c4b37614df7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1sGtmWRK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521747666459_image.png)

点击弹出菜单或进入[样式编辑器](https://www.mapbox.com/studio/styles/)选择`My Cartogram Style`。

[![](img/a7d503b7a6e47f74e41cbd1d3e155054.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b54121da--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521747365473_image.png)

## 2。挤压建筑物

你现在在**地图框样式编辑器**中。在你的左边是层列表——通过使用`Filter layers`选项并在搜索栏中键入`buildings`来寻找`buildings layer`。

[![](img/039bd75de7bab75d9cf7b83fe1400488.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8oK4R9gp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521748611617_image.png)

点击进入建筑物层，然后点击`Select Data`。

[![](img/bd45e487a0604796b81093d53c612fc3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MGVpRpUj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521848779257_image.png)

您现在看到的是 x 射线模式，在这种模式下，您可以检查底层数据——毕竟，矢量地图只是几何图形的集合！我跑题了。从`Type`下拉菜单中，选择`Fill extrusion`。当弹出窗口出现时，点击`Okay`

[![](img/d9f996534ddaab0c8cd02fcfafb16be6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h2693o3T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521849206403_image.png)

在同一面板的底部，点击`Add filter`并选择`Extrude`。添加一个新的过滤器值，使过滤器显示为`Extrude is any of true`。要查看真/假选项，请点击`Empty` -

[![](img/5158856b87dc272455ffb0a947067525.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--loeFoy11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522079896715_image.png)
[![](img/b9239a3475e896b692bb2ef8c362c8b7.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--5qm6kvnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522080190706_image.png)

## 3。设计您的建筑

切换回你的建筑物层的`Style`标签,( 1)点击进入`Height` (2)选择`Value options`，然后(3)选择`Set value by data`
[![](img/23ae575c022876f84a228e3db789d87b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HBAD64Vi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522072955238_image.png)

现在我们将告诉我们的建筑应该有多高。在`Field`下拉菜单中，选择`Height`。将你的`Default value`设置为 15，点击底部的`Add a Stop`。在`Height`和`Fill` `Height`框中输入一个数字，如 3000。

[![](img/6ff88b96124215734c5bbac3737a5b73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w-czSwTz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522079588818_image.png)

放大你的地图，你会看到建筑开始出现。按住 control 键的同时单击并拖动以倾斜和旋转地图，从而查看不同的视角。

现在让我们通过选择`Color`并从选择菜单中选择一种阴影来改变建筑的风格。

[![](img/c88f22c286e3ffeb64a2226a44c4a6bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L3b5nOwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522074037415_image.png)

在我们开始对我们的建筑进行最终设计之前，使用右边面板上的`Search box`键入一个有着突出天际线的城市，如多伦多、旧金山或纽约。

[![](img/0c2a4166cee51de7a0f1dc97baf0b524.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BZS2Zut2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522074225693_image.png)

## 最终造型-调整颜色&灯光

在图层列表中，将`buildings`图层在地图层次中向上拖动，将其放在所有道路标签图层(`road-label`、`-large`、`road-label-medium`、`road-label-small`)的正上方。如果您只看到左侧面板上的`buildings`层，单击* `X`查看所有层。

[![](img/3f406a5486fe6389756e9d4d6a367f4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2t6tSr---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522080458957_image.png)

继续根据你的审美来调整建筑物的颜色和光线强度。

[![](img/07cbc354849748358f7d46ca85a6a566.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2n0GECaG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1522080705437_image.png)

按住`control`并按住点击来旋转最终截图的地图的倾斜度。你做到了！

[![](img/7ca43bb14aa26fe237ab13315176c5cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ct7dwW7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_041B4F442AB3FA44009E04E6B6969CD4B6403CC4F79D767F4AE8CAB85D42DA98_1521853860436_image.png)

💥用#mapmadness18 在@mapbox 上发布您的截图🏀

我们第一次用户大会的免费门票不是你想要的？用 [Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/example/simple-map/) 在野外适当扩展这个地图。