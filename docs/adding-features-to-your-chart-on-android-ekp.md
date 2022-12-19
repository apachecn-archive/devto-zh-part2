# 在 Android 上向图表添加功能

> 原文：<https://dev.to/brightdevs/adding-features-to-your-chart-on-android-ekp>

在[之前的帖子](https://brightinventions.pl/blog/charts-on-android-1/)中，我确实向你展示了如何使用 [MPAndroidChart](https://github.com/PhilJay/MPAndroidChart) 库对 Android 线性图表进行基本设置。现在，我将向您展示一些我认为有用的功能。

[![tuning](../Images/baad09c1b3ce5249f4a00bdcf5354af6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ovF1SEBq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5m5f5ue5qxhyhy26l27h.jpg)

## 自定义处理图表点击事件

该库提供了默认的轻按或拖移高亮显示值行为。你可能想自己处理，所以熟悉一下`OnChartValueSelectedListener`。它包含两种方法:

*   `onNothingSelected`
*   `onValueSelected`

`onValueSelected`提供了`Entry`对象，让您可以访问数据，在我们的例子中就是`FoodSearch`。让我们用它来做一些简单的事情，比如在 toast 中显示一些信息，看看它是如何工作的:

```
lineChart.isHighlightPerTapEnabled = true

lineChart.setOnChartValueSelectedListener(object : OnChartValueSelectedListener {

    override fun onNothingSelected() {}

    override fun onValueSelected(e: Entry, h: Highlight) {
        val yearWeek = (e.data as FoodSearch).week_id
        Toast.makeText(baseContext, "date: $yearWeek", Toast.LENGTH_SHORT).show()
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

[![highlight chart](../Images/9605c6e9321a01240fc181be07d12265.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OchFhEpC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2bpy4m9adl4ageq1aasx.png)

如果你想在选中的条目上显示一些视图，你可以利用`onValueSelected`方法中的`Highlight`对象，通过获取`xPx`和`yPx`属性并将其设置为视图来获得条目在屏幕上的坐标。

## 现场数据

> MPAndroidChart 并不正式支持实时数据，** *然而* *(...)*

只要我们保持对特定的`DataSet`对象的引用，我们就能够动态地添加和删除条目。这可能是从互联网上同步数据的结果，或者是一些异步任务的部分进展——没关系。让我们在单击按钮时添加一个随机条目。

```
val rand = Random()
bananaDataSet.addEntry(Entry(bananaDataSet.entryCount.toFloat(), rand.nextFloat() * 100))
yogurtDataSet.addEntry(Entry(yogurtDataSet.entryCount.toFloat(), rand.nextFloat() * 100)) 
```

Enter fullscreen mode Exit fullscreen mode

修改数据集时，需要通知`LineData`对象和图表。为此，对它们调用适当的方法，然后像处理任何其他视图元素一样简单地使图表无效。

```
lineChart.data.notifyDataChanged()
lineChart.notifyDataSetChanged()
lineChart.invalidate() 
```

Enter fullscreen mode Exit fullscreen mode

我建议您查看一下在[文档](https://github.com/PhilJay/MPAndroidChart/wiki/Dynamic-&-Realtime-Data)中描述的更新图表数据的其他方法。

## Viewport

假设您想把可读性放在一边，删除带有所有空白的标签和轴，让一个纯图表视图充满屏幕。标签/轴配置是显而易见的，只需禁用它们:

```
lineChart.axisLeft.isEnabled = false
lineChart.axisRight.isEnabled = false
lineChart.xAxis.isEnabled = false
lineChart.description.isEnabled = false
lineChart.legend.isEnabled = false 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这并不能完成任务。正如您在下面看到的，图表和左侧父视图之间仍有空间。

[![default viewport](../Images/96fffea07262fcd7e3d55c2f210722a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X5R_ruM7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oaytthx66dckdbssy0uo.png)

如何禁用图表填充？该库为我们提供了一种简单的方法来修改图表的`Viewport`。在这种情况下，调用图表对象上的方法`setViewPortOffsets(0f,0f,0f,0f)`。它将图表的偏移量(填充)设置为 0。请记住，在数据设置好之后，所有的视口修改都必须被调用**。**

##### 但是...！

这是一个你需要谨慎对待的解决方案。关于使用这种方法，文档是怎么说的？

> 只有当你知道你在做什么的时候才使用这个。

为什么？在一次对图表视窗的短暂探索中，我遇到了一个问题。偏移似乎被独立地重新计算，除了图表本身之外，其他所有视图都不知道这个变化。这可能会导致一些渲染问题，例如:

[![viewport issue](../Images/426df1dfcbb7bde72dd1fc413a253ced.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qEJM-MfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl4rmiubky1afc8mgcfy.png)

显示的值是所提到的“其他视图”,它不知道偏移的变化，所以边缘值被剪切。请记住这一点修改视口！更多的视口修改，请查看项目的 [wiki 页面](https://github.com/PhilJay/MPAndroidChart/wiki/Modifying-the-Viewport)。

## 可提取值

如果你需要以某种特殊的方式标记任何值，你可以定义`Entry`的`icon`属性。这是一个`Drawable`类的实例，所以很容易在资源文件中布局图标，然后将其应用到条目中。在我们的例子中，让我们用一个红点来标记与前一个值相差超过 *15* 的每个值，以查看图表上每个更大更陡的斜率。

定义任何可绘制的资源，例如 20x20 红色椭圆形:

```
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <size android:width="20dp" android:height="20dp" />
    <solid android:color="#ff0000" />
</shape> 
```

Enter fullscreen mode Exit fullscreen mode

现在给`getEntriesFromCSV`方法添加一些代码。在添加一个新的`Entry`到一个数组之前，如果这个点满足条件:
，添加 Drawable 作为图标的属性

```
 data?.mapIndexed { index, foodSearch ->
    val entry = Entry(index.toFloat(), foodSearch.value.toFloat(), foodSearch)
    val steeperSlope = 15

    if (index != 0 && (foodSearch.value > data?.get(index - 1)!!.value + steeperSlope ||
                    foodSearch.value < data?.get(index - 1)!!.value - steeperSlope)) {

        val icon = resources.getDrawable(R.drawable.ic_balloon, null)
        entry.icon = icon
    }

    entries.add(entry)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了看到图标，在每个`DataSet`对象:
上设置 *drawIcons* 标志为`true`

```
bananaDataSet.setDrawIcons(true)
yogurtDataSet.setDrawIcons(true) 
```

Enter fullscreen mode Exit fullscreen mode

感谢图标显示在指定条目上:

[![icons over values](../Images/ac2ffb7321f2ce8d72f6b8aba6bbdbaf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LNYIYE9Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8fye3hexlqjvkwpp0g0l.png)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件工程师拉德克·pieczątkiewicz @光明发明
[电子邮件](//radek.pieczatkiewicz@brightinventions.pl)