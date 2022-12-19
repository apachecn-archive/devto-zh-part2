# 使用 Mapbox 和 Scenekit 构建 3D 地图

> 原文：<https://dev.to/rockarts/build-a-3d-map-using-mapbox-and-scenekit-515p>

在 Mapbox Explore Outdoors 2018 挑战赛的最终挑战中，我们被要求创建一个我们家乡 poi 的 3D 地图。为了这次挑战，我创建了班夫国家公园(又名 Reddit 湖)中 10 座山峰的[山谷的 3D 可视化。](https://en.wikipedia.org/wiki/Valley_of_the_Ten_Peaks)

[![By Gorgo - Photo taken by author, Public Domain, https://commons.wikimedia.org/w/index.php?curid=334627](../Images/2bdb1f834cd1942460caa06fa005acf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--owzv6_BK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f0ixk69yoel9unq7jujm.jpg)

为了让你兴奋，这是最终产品的样子(gif 可能需要一点时间加载)！

[![3D Map](../Images/4b7333521279019fd059ffdf748b9e53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xa9oQVsu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u3ayd36dc813zlhhdjfo.gif)

首先，让我们创建一个名为 ExploreOutdoors 的单视图应用程序。关闭 XCode，导航到您的项目目录并创建这个 podfile。