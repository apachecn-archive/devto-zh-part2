# SHIFT 键上的 Oracle 地图和 MarqueeZoomTool

> 原文：<https://dev.to/isabolic99/-oracle-maps-and-marqueezoomtool-on-the-shift-key--g1d>

#### Oracle 地图简介

Oracle maps(简称 OM)不是最常用的地图 javascript 库，但它非常适合显示地理参考数据。大多数开发人员在使用地图时都会求助于 javascript 库，比如传单和 OpenLayers。Oracle 地图最适合与 Oracle 地图查看器配合使用，它对 OGC 标准有一些支持，因此您可以从 geoserver 添加图层，但不要期望它们的高级用法。

#### MarqueeZoomTool

缩放工具是 OpenLayers 中的标准工具。在鼠标上按住左键**并按下 **SHIFT** 键来绘制矩形，地图会自动缩放到所绘制的特定区域。在下面的例子中，你可以用 OL 试试这个。**

[https://codepen.io/isabolic99/embed/eVrddd%20?height=500&default-tab=result&embed-version=2](https://codepen.io/isabolic99/embed/eVrddd%20?height=500&default-tab=result&embed-version=2)

在不是标准工具的 OM 中， **SHIFT 键没有实现**，有 3 种类型的行为。

om . tool . marqueezoomtool . one _ TIME-仅一次，一旦工具处于活动状态，它将在缩放完成后自行停用。

om . tool . marqueezoomtool . continues-该工具始终处于活动状态，因此地图上没有平移/拖动控件。

提示-该工具始终处于活动状态，你画一个矩形并点击它进行缩放，如果你点击其他地方，你取消缩放。

#### MarqueeZoomTool on **SHIFT 键**

所以在 OM 中，我不得不做一个简单的修改，用 SHIFT 键来实现这个功能。带有一次性选项的实例化 MarqueeZoomTool。

之后，我在 map 对象的$oracleMapDiv 属性上添加了事件处理程序“mousedown”。$oracleMapDiv 属性实际上是 jquery 选择器，因此“on”方法可用于事件注册。

如果禁用地图拖动动作(map.enableMapAction.drag = false)并通过调用 **"start()"** 和 **"begin(e)"** 绘制矩形进行缩放来激活 marqueeZoomTool，则事件处理函数检查是否按下了 SHIFT 键。

```
 var map = new OM.Map(
                document.getElementById('map'),
                {mapviewerURL: baseURL}
           );

var marqueeZoomTool = new OM.tool.MarqueeZoomTool(
                       map,  OM.tool.MarqueeZoomTool.ONE_TIME
                  );

marqueeZoomTool.allowZeroSize = true;

map.$oracleMapDiv.on("mousedown", function (e) {
    if(e.shiftKey){
      map.enableMapAction.drag = false;
      marqueeZoomTool.start();
      marqueeZoomTool.begin(e);
    }
}); 
```

#### 例子

通常我会提供 codepen/jsfiddle/jsbin 中的例子。但我没有找到任何有效的 OM.js 公共地图查看器或链接，所以我提供了截图。:/

[![example of MarqueeZoomTool on OM](img/55d752a870dcd4f878678334e53a28de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JcLBcNiJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kru5gcirw2cyb4zc7udt.png)

#### 一些有用的 OM 链接

*   [Oracle Maps html 5 Javascript API 参考](http://www.nepacoservice.com/i512/)
*   [Oracle maps 旧 api](http://www.belorusneft.by/mapviewer/fsmc/apidoc/index.html)
*   [其他示例和教程](http://oracle-maps.blogspot.hr/2009/09/mapviewer-code-samples-tutorials-and.html)