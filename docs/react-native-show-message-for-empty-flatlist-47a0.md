# 对空平面列表的本机显示消息作出反应

> 原文：<https://dev.to/skptricks/react-native-show-message-for-empty-flatlist-47a0>

Post 链接: [React 空平面列表的本机显示消息](https://www.skptricks.com/2018/11/react-native-show-message-for-empty-flatlist-view.html)

本教程解释了当 FlatList 数据源对象中没有数据时，如何在 react 本机应用程序的 FlatList 视图中显示空消息“无数据可用”。让我们考虑这样的场景，我们必须使用服务器端呈现在 FlatList 视图中加载大量数据，在这种情况下，我们需要为请求数据的用户提供适当的消息及其相应的响应。通常我们会遇到以下情况:
当数据可用时，它会使用 renderItem prop 在平面列表视图中显示数据。
当数据不可用时，将向用户显示错误信息“无数据可用”。

[反应空平面列表的本机显示消息](https://www.skptricks.com/2018/11/react-native-show-message-for-empty-flatlist-view.html)

[![](img/e34069db590b168e82ce3438109a3891.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PWiJ5d8T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-Fwkielx1ej0/W-kZoPUrI9I/AAAAAAAACIA/TsTTW4PNaqQ0UKB5vfBjadPKY2iOgUpMgCLcBGAs/s640/empty.png)