# 宝贝，再打我一次

> 原文：<https://dev.to/odoenet/hit-me-baby-one-more-time-d0o>

我喜欢人们问我一些关于如何使用 JavaScript 的 ArcGIS API 的有趣问题。有时候，它们是我以前从未真正想过的用例，虽然我每天都在使用 API，但每个人都有不同的需求。所以当我得到一个让我去*嗯*的问题时，我会很兴奋。

最近有人问我，如何在多个地图视图中同步一个 [`hitTest`](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#hitTest) 。这很有趣，因为我从来没有真正想过这个问题。我见过有人想要同步多个视图的[视点](https://developers.arcgis.com/javascript/latest/sample-code/views-synchronize/index.html)，但是一个`hitTest`，也就是在多个视图间的点击，让我陷入了思考。

首先想到的是一个`hitTest`带一个 [`screenPoint`](https://developers.arcgis.com/javascript/latest/api-reference/esri-geometry-ScreenPoint.html) ，是相对于视图左上角的 x 和 y 位置。通常，这很酷，但如果你的应用程序中有两个地图视图，第一个地图视图的`screenPoint`与第二个地图视图的`screenPoint`不同，因为它们在浏览器窗口中的位置不同。

幸运的是，视图中有几个助手方法可以帮助解决这个问题。还有 [`toMap`](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#toMap) 和 [`toScreen`](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#toScreen) 。你能做的，就是把`screenPoint`转换成`mapPoint`，然后用第二个视图，把`mapPoint`转换成新的`screenPoint`，现在你可以用那个`screenPoint`作为你第二个视图的`hitTest`。

跟随？

以下是步骤。

1.  点击视图 1
2.  在视图 1 上执行`hitTest`
3.  将`sreenPoint`转换为`mapPoint`
4.  将`mapPoint`转换为新的`screenPoint`
5.  在视图 2 上执行`hitTest`

您可以通过创建几个通用函数来实现这一点。

```
 function hitView(mapPoint, view, layer) {
    const sp = view.toScreen(mapPoint);
    view.hitTest(sp)
      .then(result => {
      view.whenLayerView(layer).then(lview => {
        const gs = result.results.map(x => x.graphic);
        lview.highlight(gs);
      });
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数通过将`mapPoint`转换为`screenPoint`在给定的视图和层上执行`hiTest`。然后，它会完成在地图上高亮显示要素的步骤。

现在你需要添加一些点击事件到你的视图中，所以让我写一个通用的函数。

```
function onClick(v1, v2, lyr1, lyr2) {
  v1.on("click", event => {
    event.stopPropagation();
    hitView(event.mapPoint, v1, lyr1);
    hitView(event.mapPoint, v2, lyr2);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

第二种方法采用两组视图和层。然后它调用`event.stopPopagation()`到没有显示的弹出窗口，并将`mapPoint`、视图和层传递给我们刚刚创建的`hitView`方法。

现在我们只需要将这个方法应用到我们当前的视图和图层中。

```
onClick(view1, view2, flayer1, flayer2);
onClick(view2, view1, flayer2, flayer1); 
```

Enter fullscreen mode Exit fullscreen mode

这将使`hitTest`在两个不同的地图视图中保持同步。

你可以在这里看到这个例子。

[https://codepen.io/odoe/embed/wEwYgz?height=600&default-tab=result&embed-version=2](https://codepen.io/odoe/embed/wEwYgz?height=600&default-tab=result&embed-version=2)

您也可以观看一个视频，在那里我一步一步地讲述了这个代码示例。

[https://www.youtube.com/embed/uJQAa-1HxPE](https://www.youtube.com/embed/uJQAa-1HxPE)

你会注意到我对上面视频中的代码进行了一点重构，但是我现在更喜欢它了！

向前冲，地理黑客我的朋友！

如果你喜欢这篇文章，别忘了订阅！