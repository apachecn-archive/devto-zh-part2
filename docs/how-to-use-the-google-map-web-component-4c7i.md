# 如何使用谷歌地图网络组件

> 原文：<https://dev.to/thisisbinh/how-to-use-the-google-map-web-component-4c7i>

每个人都使用谷歌地图，至少我认识的人是这样。它很方便，无处不在，而且准确(大多数时候)。为了将谷歌地图集成到你的网站上，你需要很好地了解 [Maps JavaScript API](https://developers.google.com/maps/documentation/javascript/tutorial) 。现在，随着 Web 组件的发明，这就是一个漂亮的谷歌地图窗口所需要的一切:

```
<google-map api-key="AIzaSyD3E1D9b-Z7ekrT3tbhl_dy8DCXuIuDDRc"></google-map> 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![](img/c63b178652c2704ac657b02b7bee1630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GouncZvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvn2sif5xi3v30n5617p.png) 
好得难以置信？这就是你的网络组件😉让我们看一些关于如何利用这个强大的 web 组件的例子。

## 在谷歌地图中显示标记和多边形

web 组件 [`google-map`](https://vaadin.com/directory/component/googlewebcomponentsgoogle-map) 还提供了一些其他有用的子元素:

*   `google-map-directions`:提供[方向 API](https://developers.google.com/maps/documentation/javascript/directions) 服务。
*   `google-map-marker`:提供地图标记。它被用作谷歌地图的子地图。
*   `google-map-point`:提供一个地图点。可以作为所有 google-map-*的子代。
*   `google-map-poly`:显示一系列相连的线段。它在谷歌地图内使用，需要至少两个谷歌地图点。
*   `google-map-search`:提供[位置 API](https://developers.google.com/places/web-service/intro) 功能。

例如，下面是如何使用标记(单击 HTML 显示代码):
[https://codepen.io/vaadin/embed/bjNweW?height=600&default-tab=result&embed-version=2](https://codepen.io/vaadin/embed/bjNweW?height=600&default-tab=result&embed-version=2)
从代码中可以看出，标记需要经度和纬度值。您可以在一张地图中包含尽可能多的标记，属性`fit-to-markers`确保所有标记都在视图中。嗯，它在我的演示中不起作用。

`google-map-point`和`google-map-marker`有什么区别？一个标记可以直接显示在`google-map`里面，可以拖动，而一个点是固定的，只能作为`google-map-*`的子节点，比如`google-map-poly` :
[https://codepen.io/vaadin/embed/rraWzO?height=600&default-tab=result&embed-version=2](https://codepen.io/vaadin/embed/rraWzO?height=600&default-tab=result&embed-version=2)

我无法成功地使`google-map-directions`和`google-map-search`工作，几个月前当我第一次尝试这些时，同样的事情发生了。请随意在此处查看和叉笔:

*   `google-map-directions`:[https://codepen.io/vaadin/pen/NBPbVQ](https://codepen.io/vaadin/pen/NBPbVQ)
*   `google-map-search`:[https://codepen.io/vaadin/pen/mjyRXZ](https://codepen.io/vaadin/pen/mjyRXZ)

如果你成功了，请在下面留言教我:)

## 最后的话

随着 Web 组件的发明，开发人员在网站中集成谷歌地图等技术可能会容易两步。Web 组件最好的一点是，在几年内，它的所有标准都可以在每个浏览器中本地使用。

*   *更多关于 Web 组件的新闻和文章，请在 Twitter 上关注我 [@binhbbbb](https://twitter.com/binhbbbb)*
*   如果你想尝试 Web 组件，请查看系列 [Web 组件星期三](https://vaadin.com/blog?tag=wcw)，在那里我向初学者介绍并解释了易于使用的 Web 组件。