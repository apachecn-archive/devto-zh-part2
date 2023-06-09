# 您在 Android 应用程序中使用 CSV 解析器的第一个图表

> 原文：<https://dev.to/brightdevs/your-first-chart-in-android-app-with-csv-parser-4eo1>

如果你曾经需要给你的安卓应用添加图表，你肯定听说过由[菲尔杰](https://github.com/PhilJay)创作的 [MPAndroidChart](https://github.com/PhilJay/MPAndroidChart) 。如果没有，可以考虑使用这个强大的库。让我告诉你开始有多容易！

[![header img](img/67bfca112d50abdc36d1658dd7488c49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VT0tdqdB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wgjlhkvutbetbc1oxjnk.jpg)

### 目标

我们的目标是构建一个用 Kotlin 编写的简单应用程序，它显示一个包含静态数据的线性图表。为了让它更有趣一点，我们将用`.csv`文件提供数据。这是一种非常简单的格式，用于以文本文件的形式存储基于表格的数据，文本文件中的值用逗号分隔(逗号分隔值)。我们将使用一个 [`OpenCSV`](http://opencsv.sourceforge.net/) 库来解析它。

### 依赖关系

首先添加依赖关系到 gradle 文件。

```
allprojects {
    repositories {
        ...
        maven { url "https://jitpack.io" } 
```

Enter fullscreen mode Exit fullscreen mode

```
dependencies {
    ...
    implementation "com.github.PhilJay:MPAndroidChart:v3.0.3"
    implementation "com.opencsv:opencsv:4.1" 
```

Enter fullscreen mode Exit fullscreen mode

### 原始数据集

找到一些数据，比如从[这里的*到*这里的](https://www.kaggle.com/datasets)。我选择了 Google 上的 [*食物搜索*](https://www.kaggle.com/GoogleNewsLab/food-searches-on-google-since-2004) 集合，并对其进行了一点剪切，以显示两个搜索的对比:`banana bread`和`frozen yogurt`。文件看起来是这样的:

```
id,googleTopic,week_id,value
banana-bread,/m/04cym9,2004-01,30
banana-bread,/m/04cym9,2004-02,31
banana-bread,/m/04cym9,2004-03,24
banana-bread,/m/04cym9,2004-04,27
... 
```

Enter fullscreen mode Exit fullscreen mode

两个数据集中有`676`条记录。值是归一化的，所以`value`在`0`和`100`之间，其中`0`是搜索次数最少的，`100`是最大的，其余按比例计算。

将`banana_bread.csv` & `frozen_yogurt.csv`放在项目中的`/app/res/raw`目录下。

现在让我们用相应的字段制作一个`data class`，尽可能简单:

`FoodSearch.kt`

```
package com.bi.chartapp

data class FoodSearch(
        val id: String,
        val googleTopic: String,
        val week_id: String,
        val value: Int
) 
```

Enter fullscreen mode Exit fullscreen mode

### 如何解析？

看看下面的解析器:

`Parser.kt`

```
package com.bi.chartapp

import com.opencsv.CSVReaderBuilder
import java.io.Reader

class Parser {

    companion object {

        fun toDataSet(reader: Reader): List<FoodSearch> {

            val csvReader = CSVReaderBuilder(reader)
                    .withSkipLines(1)
                    .build()

            val foodSearches = mutableListOf<FoodSearch>()
            var record = csvReader.readNext()

            while (record != null) {
                foodSearches.add(FoodSearch(record[0], record[1], record[2], record[3].toInt()))
                record = csvReader.readNext()
            }

            return foodSearches
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

静态方法基于提供的`Reader`构建一个`FoodSearch`记录列表。你可以从`fileStream`获得一个阅读器，里面有活动资源。看起来是这样的:

```
val streamBananas = resources.openRawResource(R.raw.banana_bread)
val bananaData = Parser.toDataSet(streamBananas.reader()) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的数据看起来更友好一点。该做图表了！

### 应用数据

将`LineChart`视图添加到布局中，例如:

```
<com.github.mikephil.charting.charts.LineChart
        android:id="@+id/lineChart"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_centerInParent="true" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在如何管理数据？

`LineChart`接受数据作为`LineData`类的实例。`LineData`至少由`LineDataSet`的一个实例创建。`LineDataSet`是由`Entry`对象的`MutableList`和`String`标签创建的。哼。而`Entry`是表示我们图表上单点的对象(基本上是一个(x，y)表示)。起初这听起来有点让人不知所措，但是非常简单。以下是要遵循的步骤:

1.  将您的数据从 csv 文件解析为表示图表上的单点的任何数据对象的列表——完成！
2.  将数据对象列表映射到`Entry`对象列表
3.  从条目列表中创建`LineDataSet`和标签，比如“香蕉面包”
4.  使用每个集合作为参数来创建`LineData`对象。瞧。

```
 private fun getEntriesFromCSV(rawResId: Int, label: String): LineDataSet {

    var data: List<FoodSearch>? = null
    resources.openRawResource(rawResId).use { stream ->
        data = Parser.toDataSet(stream.reader())
    }
    val entries: MutableList<Entry> = ArrayList()

    data?.mapIndexed { index, foodSearch ->
        entries.add(
                Entry(index.toFloat(), foodSearch.value.toFloat(), foodSearch)
        )
    }

    return LineDataSet(entries, label)
} 
```

Enter fullscreen mode Exit fullscreen mode

上一段中的这个函数和解析器从`csv`资源创建`LineDataSet`。现在简单地为每个`csv`文件调用它并创建`LineData` :

```
val bananaDataSet = getEntriesFromCSV(R.raw.banana_bread, "Banana Bread")
val yogurtDataSet = getEntriesFromCSV(R.raw.frozen_yogurt, "Frozen Yogurt")

lineChart.data = LineData(
        bananaDataSet,
        yogurtDataSet
) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，图表是用数据建立起来的！
[![chart one](img/ba6afd8200dabb9807fd8b544eed6fbd.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JievjCsK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30c1i599qf8ccoac0usw.png)

### 让我美丽-折线图属性

是时候添加一些颜色和香料了。图表布局配置非常灵活。我将向您展示一些基本属性，其余的您可以在[文档](https://github.com/PhilJay/MPAndroidChart/wiki)中找到。属性分为特定于数据集的属性和特定于图表的属性。

##### 调优数据集

首先给资源`colors.xml`文件添加一些颜色，让香蕉看起来更像香蕉和酸奶酸奶。

```
<color name="banana">#ffe100</color>
<color name="yogurt">#0085c7</color> 
```

Enter fullscreen mode Exit fullscreen mode

我们将以同样的方式配置每个`LineDataSet`对象，但是使用不同的颜色。查看下面的方法，并附上评论:

```
private fun configureSetLayout(set: LineDataSet, color: Int) {

    set.color = color                         // color of the line
    set.setDrawFilled(true)                   // fill the space between line and chart bottom
    set.fillColor = color                     // color of the fill
    set.setDrawCircles(false)                 // disable drawing circles over each Entry point
    set.mode = LineDataSet.Mode.CUBIC_BEZIER  // round the line
    set.fillAlpha = 50                        // make fill transparent with alpha (0-255)

} 
```

Enter fullscreen mode Exit fullscreen mode

现在将配置应用于每个数据集:

```
val bananaColor = resources.getColor(R.color.banana, null)
val yogurtColor = resources.getColor(R.color.yogurt, null)

configureSetLayout(bananaDataSet, bananaColor)
configureSetLayout(yogurtDataSet, yogurtColor) 
```

Enter fullscreen mode Exit fullscreen mode

##### 调一个图表

您可以通过多种方式配置图表行为。默认设置允许用户通过挤压和滚动来缩放图表。由于我们的数据集包含约 700 条记录，因此我们保留沿 X 轴缩放图表的功能，仅阻止沿 y 轴缩放图表的功能。另外，删除右下角的描述，并通过点击突出显示值。就像这样:

```
lineChart.description.isEnabled = false

lineChart.isHighlightPerTapEnabled = false
lineChart.isHighlightPerDragEnabled = false
lineChart.isScaleYEnabled = false 
```

Enter fullscreen mode Exit fullscreen mode

它已经更加直观地工作了。现在请注意，顶部 X 轴上方的描述是浮点值。不幸的是，他们没有说太多。如果他们每 52 周标记一次一年的变化就更好了！为了达到这个目的，使用`IAxisValueFormatter`。看起来是这样的:

```
inner class MyAxisFormatter : IAxisValueFormatter {
    override fun getFormattedValue(value: Float, axis: AxisBase?): String {
        return if (value.toInt() % 52 == 0) "${startingYear + value.toInt() / 52}"
        else ""
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着它将显示一个能被 52 整除的值。然后将该值映射到相应的年份(起始年份设置为 2004)。我们还需要一个适当的粒度，这样就不会有任何年份之间的网格。

```
lineChart.xAxis.valueFormatter = MyAxisFormatter()
lineChart.xAxis.granularity = 52f 
```

Enter fullscreen mode Exit fullscreen mode

谁还需要两边都有 Y 轴呢？禁用其中一个:

```
 lineChart.axisRight.isEnabled = false 
```

Enter fullscreen mode Exit fullscreen mode

嘣！就是这样！看起来不错，并且清楚地呈现数据。捏缩放，左右滑动来浏览所有这些年，并最终在谷歌搜索中检查冷冻酸奶何时击败香蕉面包！

[![chart two](img/b008e7cc0298ea7b2b56371c976840d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y0smC1Q---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61s0l8dkmxq9l83svlip.png) 

哦，好像每年夏天前后！谁会知道。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件工程师拉德克·pieczątkiewicz @光明发明
[电子邮件](//radek.pieczatkiewicz@brightinventions.pl)

### 参考文献:

[维基](https://github.com/PhilJay/MPAndroidChart/wiki) & [问题](https://github.com/PhilJay/MPAndroidChart/issues)