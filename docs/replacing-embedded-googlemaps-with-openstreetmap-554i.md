# 用 OpenStreetMap 替换嵌入式 GoogleMaps

> 原文：<https://dev.to/mikeralphson/replacing-embedded-googlemaps-with-openstreetmap-554i>

最近，谷歌[宣布](https://cloud.google.com/maps-platform/user-guide/)从 2018 年 6 月 11 日起，计费账户(信用卡/借记卡)和 API 密钥将成为使用谷歌地图的强制性要求，即使你的使用量目前低于每月 200 美元的免费水平。

然而，如果您觉得这是一个不必要的复杂问题，并且希望避免令人不快的意外，特别是如果其他人要使用您的 API 密钥，那么可能是时候研究一个替代方案了，比如 [OpenStreetMap](https://www.openstreetmap.org/) 。

我的用途只是在我的网站上提供一个动态位置地图，所以步骤对我来说特别简单:

*   前往[OpenStreetMap.org](https://openstreetmap.org)
*   搜索您想要作为地图中心的位置，或使用箭头显示我的位置
*   选择所需的层，如标准、循环图、运输等
*   使用+和-图标和鼠标，根据需要缩放和平移
*   选择共享图标
*   决定是否要显示标记，如果要，勾选复选框
*   突出显示 HTML 选项卡
*   将生成的 HTML 片段复制并粘贴到您的网站上

例如:

```
<iframe width="425" height="350" frameborder="0" scrolling="no" 
marginheight="0" marginwidth="0" 
src="https://www.openstreetmap.org/export/embed.html?bbox=-2.493209838867188%2C53.50540525319918%2C-2.246360778808594%2C53.61980121473449&amp;layer=mapnik&amp;marker=53.56274386269267%2C-2.3699569702148438" 
style="border: 1px solid black"></iframe>
<br/><small>
<a href="https://www.openstreetmap.org/?mlat=53.5627&amp;mlon=-2.3700#map=12/53.5626/-2.3698">
View Larger Map</a></small> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，关键要素是所需地图区域边界框的经纬度坐标。`%2C`是 URL 编码的逗号。您还可以根据需要调整`iframe`的宽度和高度等。

[![Map example](../Images/109fc285bceb82ebd97583dca59f5741.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oEjrI_gi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umeqigyohfvazmko5p8q.png)

快乐映射。