# ArcGIS JS API 应用程序中的自定义书签

> 原文：<https://dev.to/odoenet/custom-bookmarks-in-your-arcgis-js-api-apps-37oc>

最近发布的 [ArcGIS API for JavaScript](https://developers.arcgis.com/javascript/latest/index.html) 引入了[书签小部件](https://developers.arcgis.com/javascript/latest/sample-code/widgets-bookmarks/index.html)，以支持您可以[在 WebMaps](http://doc.arcgis.com/en/arcgis-online/create-maps/bookmark.htm) 中创建的书签。

这是一个非常酷，简单而优雅的小部件。更不用说，如果用户在网络地图中创建书签，在定制应用程序中向他们展示这一点是很酷的。

*但是*，为什么要满足于你能用网络地图做的事情。你想在你自己的应用程序中加入书签，你想利用这个很酷的新工具。但是等一下，你怎么保存这些书签呢？只要坚持使用[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)，不要担心它。

那么你会怎么做呢？

您可以创建一个带有空书签数组的 WebMap。

```
const map = new WebMap({
  basemap: "streets",
  bookmarks: []
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`esri/WebMap`，因为它上面有`bookmarks`的属性。没有规定说您需要用 webmap id 初始化 WebMap，您可以像使用`esri/Map`一样使用它并提供底图和图层。

现在，你所能做的就是点击一个按钮或其他事件，完全由你决定，向书签集合添加一个书签。

```
const bookmark = {
  extent: view.extent,
  name: `Bookmark: ${bookmarks.bookmarks.length + 1}`
};
bookmarks.bookmarks.add(bookmark); 
```

Enter fullscreen mode Exit fullscreen mode

我只是根据已经存在的书签数量来创建名称。你可以有一些用户界面，让用户提供一个自定义名称或得到*花式*和做一些 reverese 地理编码，以获得一个地址的范围或城市，县，无论浮动你的 geoboat。

现在，我可以将书签添加到本地存储中。

```
const rawBookmarks = bookmarks.bookmarks.map(({ active, extent, name, thumbnail }) => ({ active, extent, name, thumbnail }));
const localData = localStorage.setItem(BOOKMARK_KEY, JSON.stringify(rawBookmarks)); 
```

Enter fullscreen mode Exit fullscreen mode

要在应用程序重新加载时访问这些存储的书签，您可以这样做。

```
let existingData = [];
const existingBookmarks = localStorage.getItem(BOOKMARK_KEY);
if (existingBookmarks) {
  existingData = JSON.parse(existingBookmarks);
}
const map = new WebMap({
  basemap: "streets",
  bookmarks: existingData
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将在应用程序重新加载时保存书签。*牛逼吧？！*

当然，您可以添加删除书签的方法，或者清除所有书签，但是我会让您自己决定。

您可以在一个[示例应用程序](https://codepen.io/odoe/pen/QxrEVX)中找到一些乐趣。

所以好好享受吧！别忘了，工具是为你准备的，你只需要做一点小小的工作就可以制作出很棒的应用程序。

尽情享受吧！

*最初发表于[odoe.net](http://odoe.net/blog/custom-bookmarks-in-your-arcgis-js-api-apps/)T3】*