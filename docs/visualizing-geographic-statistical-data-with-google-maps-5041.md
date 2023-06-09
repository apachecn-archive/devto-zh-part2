# 用谷歌地图可视化地理统计数据

> 原文：<https://dev.to/nholmber/visualizing-geographic-statistical-data-with-google-maps-5041>

本教程将教你如何创建一个自定义的基于 Google Maps 的地图来可视化地理统计数据。当开发机器学习模型时，这样的地图可以是有用的工具。作为一个具体的示例，我们将创建一个地图来显示芬兰邮政编码区域的人口密度和家庭收入中位数。

政府和其他机构将地理统计数据，如经济和人口相关统计数据，收集到国家和区域一级的数据库中。这些统计数据的公开可用性吸引了机器学习模型的开发，例如，预测未来房价将如何演变。

任何机器学习项目的关键部分都是可视化输入数据。Google Maps 是一个自然的工具，可以可视化地理方面的统计数据，因为绝大多数人已经对这个平台非常熟悉。你可能会问，为什么是谷歌地图，而不是其他服务或图书馆。我承认，这种方法对于静态数据绘图来说可能是多余的，也就是说，如果您的目的是为书面报告创建一个地图。然而，如果你想创建一个交互式网站，甚至是一个完全成熟的网络应用程序，并与他人分享，那么使用谷歌地图进行数据可视化确实很棒。

让我以展示我们将在这篇文章中构建的最终地图开始这篇文章。我还在下面放了一个 YouTube 视频短片，因为正如你可能想象的那样，截屏并不能很好地表现互动网站。你可以在 [GitHub](https://github.com/nholmber/google-maps-statistics) 上找到这篇文章中描述的完整代码，以防你想直接进入细节并复制一份地图的工作副本。在下面的小节中，我将更详细地介绍地图创建过程的主要步骤。

[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/29e2b33022865d858a8cafb855f28bdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UTPdOwIA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321555/gmaps-4_irvi3h.png)
[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/124fca0c491a7e4818d4290486cc8948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MdXcTLw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321558/gmaps-2_dmfdnn.png)
[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/663f89e2e594ff9878e4cc480d90793d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DH48qeIP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321553/gmaps-3_cc3fxg.png)
[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/c5746433f401e7bd3c90f9f5c34e9dfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F55PIxH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321554/gmaps-1_zjqilx.png)
[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/8964b0feafac911017f2a769eb170691.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzorKU7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321556/gmaps-6_v3koyw.png)
[![Showcase: Using Google Maps to visualize geographic data. Map data ©2018 Google.](img/176de32ba9e7b2af995541e3c8d4b3c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6YPEt37b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/nholmber/image/upload/v1533321555/gmaps-5_hqo9cj.png)
展示:使用谷歌地图可视化地理数据。地图数据 2018 谷歌。

[https://www.youtube.com/embed/sun6m9v6XB4](https://www.youtube.com/embed/sun6m9v6XB4)

# 前奏:谷歌地图 JavaScript API

虽然谷歌地图服务的一部分可以通过 [Python](https://github.com/googlemaps/google-maps-services-python) 客户端访问，但构建定制谷歌地图或在此类地图上构建应用程序只能通过 [JavaScript (JS) API](https://developers.google.com/maps/documentation/javascript/tutorial) 实现。该文档提供了大量示例，帮助您熟悉 API 的不同功能。不需要事先了解 JS。

在你开始构建自己的基于谷歌地图的地图之前，你需要通过在[谷歌云平台](https://cloud.google.com/maps-platform)上注册一个账户/项目来获得[一个 API 密钥](https://developers.google.com/maps/documentation/javascript/get-api-key)。注册是免费的，尽管需要信用卡，因为使用实际的谷歌地图平台每月信用限额高达 200 美元。免费配额对于小型个人项目来说应该绰绰有余:在创建和测试本文中描述的地图时，我使用了大约价值 1 美元的配额。实际的配额使用情况取决于对 JS API 的不同组件的调用次数，这可以通过在线控制台进行实时监控。如果你感兴趣，可以在[这里](https://cloud.google.com/maps-platform/pricing/sheet/)找到更大项目的价格信息。

# 在可视化之前获取和处理地理数据

正如引言中提到的，我将在这篇文章中考虑两个不同的统计数据:人口密度(居民/平方公里，2018 年的数据)和相对于全国平均水平的家庭收入中位数(2015 年的数据)。统计数据将在由芬兰邮政编码区域定义的地理区域中可视化。相关的原始数据是从由芬兰统计局托管的公共数据库下载的，该统计局收集、维护和发布大约 160 组不同的统计数据。这篇文章中使用的数据是通过[Paavo-Open data BY postal code area](https://www.stat.fi/tup/paavo/index_en.html)服务访问的，由 CC 4.0 授权。

> 强制性许可免责声明。本帖中使用的统计数据(人口、人口增长、收入中值)和邮政编码信息(姓名、边界、地区)是从 Paavo 门户网站下载的，由[芬兰统计局](http://www.stat.fi/tup/paavo/index_en.html)提供，由 4.0 在 [CC 下授权。数据访问时间是 2018 年 7 月 27 日。](https://creativecommons.org/licenses/by/4.0/deed.en)

在进行实际的数据处理过程之前，我们先来讨论一下如何将自定义数据包含在 Google Maps 中。可以保存用户创建的地理数据的高级对象是所谓的[层对象](https://developers.google.com/maps/documentation/javascript/layers)。有多种类型的图层可用，包括融合表、KML 元素和可保存任意地理空间数据的数据图层容器。我选择了最后一个选项，即数据层容器。这个对象的属性可以用 JS 直接定义和操作，但是一个方便的替代方法是从一个 [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) 文件导入数据。

你可能想知道什么是 GeoJSON 文件。GeoJSON 文件在许多方面只是一个常规的 JSON 文件，但添加了一些对象来表示地理要素，如点、线、多边形和这些属性的集合。例如，邮政编码区域可以表示为一个 MultiPolygon 对象，它是一组规则的多边形对象。有了这个对象，邮政编码区域是完全连通的还是由几个不相交的部分组成的(例如一组岛屿)都无关紧要。可以声明附加变量并将其与这些几何特征相关联，从而允许将邮政编码特定的统计数据直接编码到该对象中。为了说明这种文件格式有多棒，这里有一个缩短的 GeoJSON 文件的例子(我使用了[这个工具](http://geojson.io/#map=2/20.0/0.0)美化文件，只保留了多边形对象的两个顶点):

```
{  "type":  "FeatureCollection",  "features":  [  {  "type":  "Feature",  "properties":  {  "zip":  "00300",  "name":  "Pikku Huopalahti",  "area":  0.851105,  "pop2018":  5794,  "income":  23737,  "income_relative":  4123.18,  "pop_density":  6807.62,  "fill":  "#c7e0ed",  "fill_density":  "#840711"  },  "geometry":  {  "type":  "MultiPolygon",  "coordinates":  [  [  [  [  24.89407088925189,  60.1972300444163  ],  [  24.890401201643467,  60.197875777415035  ]  ]  ]  ]  }  }  ]  } 
```

GeoJSON 文件是一个`Features`的集合，在本例中，它对应于不同的芬兰邮政编码区。每个`Feature`都有两个主要组件:一个属性字典(`properties`)和一个`geometry`对象，该对象在 Google Maps 兼容的纬度/经度坐标中定义邮政编码区域多多边形对象。如你所见，我们在`properties`字典中定义了一堆变量:

*   邮编区的`name`、`zip`代码、地表`area`、人口(`pop2018`)、住户中位数`income`。填充这些变量的数据是从本文前面讨论的数据库中获得的。
*   `income_relative`:相对于全国平均收入的中位数(单位:欧元，正值表示高于平均收入)
*   人口密度`pop_density = pop2018/area`
*   两种填充颜色`fill`(代表相对收入)和`fill_density`(人口密度):这两种颜色将用于给谷歌地图中的邮政编码区域着色，以说明芬兰不同地区的数量差异

好了，我们已经介绍了如何在 Google Maps 中定义和包含自定义地理数据。我们已经准备好执行创建本文开头展示的地图所需的实际步骤。这是我们将采取的策略

1.  下载和处理数据
2.  将数据保存为 GeoJSON 文件并在线上传数据
3.  创建用于将 GeoJSON 导入 Google Maps 并随后可视化数据的 JS
4.  将 JS 嵌入到 HTML 文件中以创建作为网站的工作地图

让我们先仔细看看步骤 1-2。我在这些步骤中使用了标准的 Python 工具，即[Pandas](https://pandas.pydata.org/)/[geo Pandas](https://geopandas.org/)/[Numpy](https://www.numpy.org/)用于下载和处理数据，以及 [Matplotlib](https://matplotlib.org/) 用于创建配色方案，这些配色方案随后将用于给谷歌地图中的不同邮政编码区域着色。总的来说，这个过程非常简单，但是我想在下面强调一些小警告。你可以在 [GitHub](https://github.com/nholmber/google-maps-statistics/blob/master/map.py) 上找到完整的代码。

*   通过将 URL 传递给`read_file` GeoPandas 函数，地理统计数据被导入到 GeoPandas 表中。URL 表示对前面讨论的服务的数据库调用，它返回格式化为 JSON 文件的数据。我使用下面定义的`filter_columns`函数过滤掉了所有与这篇文章无关的数据列。

```
import geopandas as gpd

def filter_columns(df, keep):
  """Filter Pandas table df keeping only columns in keep"""
  cols = list(df)
  for col in keep:
    cols.remove(col)

  return df.drop(columns=cols)

def main():
  # Get statistics from Statistics Finland portal for year 2018 keeping only the selected data columns
  url = "http://geo.stat.fi/geoserver/wfs?service=WFS&version=1.0.0&request=GetFeature&typeName=postialue:pno_tilasto_2018&outputFormat=json"
  keep_columns = ['nimi', 'posti_alue', 'he_vakiy', 'geometry', 'pinta_ala', 'hr_mtu']
  data = filter_columns(gpd.read_file(url), keep_columns)

  # Rename columns
  data.rename(columns={'he_vakiy': 'pop2018', 'pinta_ala': 'area', 'nimi': 'name', 'hr_mtu': 'income', 'posti_alue': 'zip'}, inplace=True) 
```

*   在原始数据集中，代表邮政编码区域边界的坐标是在芬兰投影坐标系中定义的( [`epsg:3067`](https://epsg.io/3067) )。谷歌地图只支持纬度/经度坐标( [`epsg:4326`](http://epsg.io/4326) )，所以我不得不把坐标转换成正确的坐标系。提醒一下，坐标存储在与每个邮政编码区域相关联的 MultiPolygon 对象中。坐标系在该数据集中的`init`变量中定义(变量未在上面显示)。幸运的是，类型转换只是调用适当的 GeoPandas 函数的简单事情

```
# Convert geometry to Google Maps compatible Lat/Long coordinates
data.to_crs({'init': 'epsg:4326'}, inplace=True) 
```

*   数据集包含未定义收入中位数的邮政编码地区(-1.0 或 NaN)。这些邮政编码区在 2018 年要么没有居民，要么居民太少，以至于收入数据因隐私原因被压制。我只是将这些地区的相对收入中值设为全国平均水平，以避免谷歌地图中的可视化问题。

```
# Income data in some postal code areas might be undefined (NaNs or -1.0)
# In these areas, there are either no inhabitants at all, or too few inhabitans
# so the income data is not shown for privacy reasons
# Set the income in these regions to the national average
data.replace(-1.0, np.nan, inplace=True)
avg_income = np.nanmean(data['income'])
data.fillna(avg_income, inplace=True) 
```

*   我处理完数据并保存到磁盘后，发现保存的 GeoJSON 文件相当大(32 MB)，导入 Google Maps 的速度相对较慢。为了稍微减小文件大小，我决定将相对收入和人口密度的中位数四舍五入到小数点后两位。通过将邮政编码区域的坐标从 15 位小数四舍五入到 4 位小数，我能够实现更大的文件大小缩减。降低坐标精度似乎并没有显著降低邮政编码区域边界的质量，至少在谷歌地图中快速目测之后是这样的。我使用 GeoPandas 对前两个量进行舍入，使用 [`ogr2ogr`](https://www.gdal.org/ogr2ogr.html) 命令行工具对坐标进行舍入。GeoPandas(或者更确切地说是底层库)本身不支持降低多边形坐标的精度，与使用`ogr2ogr`工具相比，在 Python 中模拟这种行为相当麻烦。舍入后的最终文件大小为 17 MB。我认为有可能通过 GeoPandas [simplify](http://geopandas.org/geometric_manipulations.html#GeoSeries.simplify) 函数从多重多边形中移除冗余坐标来进一步减小文件大小，但是我还没有测试过这个选项。以下是我用来减小 GeoJSON 文件大小的命令

```
# Round data to 2 decimal places to reduce size of resulting GeoJSON file
data = data.round({'pop_density': 2, 'income_relative': 2}) 
```

```
ogr2ogr -f "GeoJSON" -lco COORDINATE_PRECISION=4 map_data_reduced.json map_data.json 
```

*   我用 Matplotlib 给收入和人口密度数据分配了离散的颜色，以便直观地区分这些数量在谷歌地图中不同的邮政编码区域是如何变化的。颜色是由宁滨数据分配的:观察到的数据范围首先被离散成一组 *N* 个面元，每个面元代表一个数值范围。然后将实际数据分配给这些箱，并使用一个等长的颜色映射向量将这些箱与一种颜色相关联。我使用了下面的代码片段来给人口密度数据着色。

```
def pop_density_binning(return_colors=False):
  """Return bins and color scheme for population density"""
  # First bin is 0, next is 0.1-1, ..., final is > 10000
  bins = np.array([0, 1, 2, 4, 6, 8, 10, 50, 100, 200, 500, 1000, 1500, 2000, 2500, 5000, 10000])
  cmap = plt.cm.get_cmap('Reds', len(bins)+1)
  if not return_colors:
    return bins, cmap
  else:
    colors = []
    for i in range(cmap.N):
      rgb = cmap(i)[:3] # will return rgba, we take only first 3 so we get rgb
      colors.append(matplotlib.colors.rgb2hex(rgb))

    return bins, colors

# Assign alternative colors for population density
bins, cmap = pop_density_binning()
colors = []

for i, row in data.iterrows():
  index = bins.searchsorted(row['pop_density'])
  colors.append(matplotlib.colors.rgb2hex(cmap(index)[:3]))

data['fill_density'] = Series(colors, dtype='str', index=data.index) 
```

*   为了将处理后的数据导入到 Google Maps 中，应该将数据保存为 GeoJSON 文件。事实证明，在 GeoPandas 中不可能将数据输出到预先存在的文件中，即使您打算覆盖原始文件。这种尝试会使文件写入驱动程序崩溃，并显示一条相当隐晦的错误消息。我显式删除了输出文件，以防它已经存在，以避免这种崩溃。

```
# Save data as GeoJSON
# Note the driver cannot overwrite an existing file,
# so we must remove it first
import os

outfile = 'map_data.json'
if os.path.isfile(outfile):
  os.remove(outfile)

data.to_file(outfile, driver='GeoJSON') 
```

# 创建基于谷歌地图的自定义地图

概括地说，我们已经将所有想要包含在基于 Google Maps 的自定义地图中的内容保存在 GeoJSON 文件中。我们现在已经准备好使用 Google Maps JS API 来导入和可视化这些数据。我将在标准的谷歌地图画布上添加以下元素:

1.  经过重新设计的底图，减少了混乱(标签、标记等)。)
2.  通过从 GeoJSON 文件导入数据创建的数据图层对象。该文件将邮政编码区域定义为多边形对象，包含人口和收入数据，以及根据上述属性值为这些区域着色的说明
3.  一个可点击的按钮，允许在两个数据集的可视化之间切换
4.  一个可点击的信息窗口，显示关于所选邮政编码区域的附加信息
5.  这是一个图例，直观地描述了这两个属性的值在芬兰的不同地方是如何变化的

我将逐一介绍每一步。如果你已经熟悉 Google Maps JS API，请随意跳过这里的完整代码。

## 1.重新设计谷歌地图

谷歌地图的默认视图可以用样式规则进行彻底的编辑。提供了一个方便的交互工具[在线](https://mapstyle.withgoogle.com/)。我用这个工具为中值收入和人口密度数据创建了两种不同的地图样式。样式规则作为一个 JSON 对象(下面的变量名为`style`)传递给 Google Maps 构造器，以后可以通过 [`setOptions`](https://developers.google.com/maps/documentation/javascript/reference/3/map#Map.setOptions) 函数进行修改。不同的默认可视化控件可以通过相同的界面禁用，完整列表见[此处](https://developers.google.com/maps/documentation/javascript/reference/3/map#MapOptions)。这是我使用的初始化器

```
// Initialize map
var map = new google.maps.Map(document.getElementById('map'), {
  zoom: 10,
  mapTypeControl: false,
  streetViewControl: false,
  styles: style
}); 
```

## 2.创建数据层对象并访问其属性

毫无疑问，这一步是地图创建过程中最简单的一步。假设 GeoJSON 文件被上传到公共域的某个地方，比如 GitHub，那么只需将托管文件的 URL 传递给`loadGeoJson`函数，就可以创建数据层对象。Getter 和 setter 函数可用于访问和操作数据层对象的属性。下面是我用来导入数据和设置每个邮政编码区域多边形对象的样式的代码片段。默认情况下(当`useDensity = false`时)，邮政编码区域根据相对中值收入进行着色，而实际的颜色存储在变量`fill`中。您可能还注意到，每当对邮政编码区域重新着色时，我都会在不同的画布可视化样式之间切换。

```
// Load GeoJSON that contains zip code boundaries and geographic information
// NOTE: This uses cross-domain XHR, and may not work on older browsers.
map.data.loadGeoJson('https://raw.githubusercontent.com/nholmber/google-maps-statistics/master/map_data_reduced.json')

// Colorize zip code areas based on relative median income
// or population density (changeable via dedicated button)
map.data.setStyle(function(feature) {
  if (!feature.getProperty('useDensity')) {
    var color = feature.getProperty('fill');
    var opacity = 0.7;
    map.setOptions({styles : style});
  } else {
    var color = feature.getProperty('fill_density');
    var opacity = 0.9;
    map.setOptions({styles : altStyle});
  }
  return {
    fillColor: color,
    fillOpacity: opacity,
    strokeWeight: 1,
    strokeColor: color
  }
}); 
```

## 2.用于在可视化效果之间切换的按钮

我们已经将两个数据集导入到 Google Maps 中，它们都有自己独特的可视化风格。我们需要创建一个按钮来切换数据集。单击此按钮将触发邮政编码区域的重新着色，并更新地图图例(见下文),以便显示正确的信息。为了在实践中实现这一功能，我向按钮(`google.maps.event.addDomListener`)添加了一个 DOM 监听器，它检测按钮何时被单击，并触发(`google.maps.event.trigger`)两个定制地图事件，更新多边形颜色和地图图例。这个步骤最好用实际代码来解释。注意，我们迭代了`map.data`(邮政编码区域)的特性，并更新了属性`useDensity`，如前一节所示，该属性控制多边形对象的样式。

```
// Create button for switching between visualizations
function switchVisualization(switchControlDiv, map){
  // Set CSS for the control border.
  var controlUI = document.createElement('div');
  controlUI.style.backgroundColor = '#fff';
  controlUI.style.border = '2px solid #fff';
  controlUI.style.borderRadius = '3px';
  controlUI.style.boxShadow = '0 2px 6px rgba(0,0,0,.3)';
  controlUI.style.cursor = 'pointer';
  controlUI.style.marginBottom = '22px';
  controlUI.style.textAlign = 'center';
  controlUI.title = 'Click to switch between visualization styles';
  switchControlDiv.appendChild(controlUI);

  // Set CSS for the control interior.
  var controlText = document.createElement('div');
  controlText.style.color = 'rgb(25,25,25)';
  controlText.style.fontFamily = 'Roboto,Arial,sans-serif';
  controlText.style.fontSize = '16px';
  controlText.style.lineHeight = '38px';
  controlText.style.paddingLeft = '5px';
  controlText.style.paddingRight = '5px';
  controlText.innerHTML = 'Change visualization';
  controlUI.appendChild(controlText);

  // Setup the click event listener. Button click switches
  // between two visualization schemes, which represent
  // different demographic data
  google.maps.event.addDomListener(controlUI, 'click', function() {
    // Trigger events that update Polygon colors and legend
    google.maps.event.trigger(map.data, 'setcolor')
    google.maps.event.trigger(map.data, 'updatelegend')
  });
}

// Note this section of code is inside the initMap code block

  // Create DIV for the button that switches between the two
  // data sets used to colorize the zip code areas.
  var switchControlDiv = document.createElement('div');
  var switchControl = new switchVisualization(switchControlDiv, map);

  switchControlDiv.index = 1;
  map.controls[google.maps.ControlPosition.RIGHT_TOP].push(switchControlDiv);

  // Add listener that detects when button is clicked
  // triggering a recoloring of the Polygons objects
  map.data.addListener('setcolor', function(event) {
    map.data.forEach(function(feature) {
      feature.setProperty('useDensity', !feature.getProperty('useDensity'));
    });
  })

  // Add listener that detects when button is clicked
  // triggering a redraw of the map legend
  map.data.addListener('updatelegend', function(event) {
    var useDensity;
    var accessed = false;
    // Determine which color scheme to use
    map.data.forEach(function(feature) {
      if (!accessed) {
        useDensity = !feature.getProperty('useDensity');
        accessed = true;
      }
    });
    // Clear old legend
    while (legend.hasChildNodes()) {
      legend.removeChild(legend.firstChild);
    }
    // Recreate legend with new color scheme
    createLegend(legend, useDensity);
  }); 
```

## 3.信息窗口

数据图层对象可以包含比简单地通过对相应的多边形对象进行着色而可视化的信息多得多的信息。我们可以显示额外的信息作为一个弹出信息，它可以包含任何内容。这里，我们将简单地显示保存到 GeoJSON 文件中的变量的数值。“简介”窗口被放置在位于所选邮政编码区域中心的不可见标记的顶部。多边形对象没有计算多边形中心点的本机`getCenter`函数。因此，该对象必须首先转换成具有所需功能的`LatLngBounds`对象。

```
// Code is again inside initMap

  // When the user hovers, tempt them to click by outlining zip code area.
  // Call revertStyle() to remove all overrides. This will use the style rules
  // defined in the function passed to setStyle()
  map.data.addListener('mouseover', function(event) {
    map.data.revertStyle();
    map.data.overrideStyle(event.feature, {strokeWeight: 4, strokeColor: 'gray'});
  });

  map.data.addListener('mouseout', function(event) {
    map.data.revertStyle();
  });

  // When the user selects a zip code area,
  // display info window with more detailed information
  var infowindow = new google.maps.InfoWindow();
  map.data.addListener('click', function(event) {
      createInfoWindow(map, event, infowindow);
  });

  function createInfoWindow(map, event){
    // Get properties from Data Layer to populate info window
    var name = event.feature.getProperty('name');
    var zip = event.feature.getProperty('zip');
    var income = event.feature.getProperty('income');
    var incomeRelative = event.feature.getProperty('income_relative');
    var population = event.feature.getProperty('pop2018');
    var populationDensity = event.feature.getProperty('pop_density');

    // Create content for info window
    var contentString = '<div id="content"><div id="siteNotice"></div>'+
      '<h2 id="firstHeading" class="firstHeading">' + name + '</h2>'+
      '<h3>Zip code: ' + zip + '</h3>'+
      '<div id="bodyContent" style="font-size: 12pt;" >'+
      '</br>Population (2018): '+ population +
      '</br>Median income (2015): '+ income.toFixed(2) + ' €' +
      '</br>Median income relative to national average (2015): '+ incomeRelative.toFixed(2) + ' €' +
      '</br>Population density (persons / km<sup>2</sup>): '+ populationDensity.toFixed(2) +'</p>'+
      '</div>'+
      '</div>';

    // Center info window on selected zip code area
    // Find center of zip code area by converting
    // the corresponding Polygon object to a
    // LatLngBounds object which has the getCenter function
    var bounds = new google.maps.LatLngBounds();
    var geometry = event.feature.getGeometry();

    geometry.forEachLatLng(function(point){
      bounds.extend({
        lat : point.lat(),
        lng : point.lng()
      });
    });
    var center = bounds.getCenter();

    // Create invisible marker for info window
    var marker = new google.maps.Marker({
      position: center,
      map: map,
      visible : false
    });
    // Create info window
    infowindow.setContent(contentString);
    infowindow.open(map, marker);
  } 
```

## 4.神话；传奇

好了，快到了。我们将添加到地图的最后一个对象是一个带有两种不同配色方案和标签集的颜色图例。颜色和标签是由 Python 脚本定义的，我们用它来绑定相应的统计数据。我没有在 JS 中重新创建这些变量，而是决定将这些值从 Python 传递给包含 JS 代码的 HTML 文件。通过命名的 HTML div 对象在 JS 中访问数据。有了可用的标签和颜色定义，在必要时绘制和更新图例就变得简单了。

```
// Code is again inside initMap

  // Create a color bar legend for the colored zip code areas.
  // By default, shows colors used for describing relative median income.
  // Button click toggles changes data to population density.
  var legend = document.getElementById('legend');
  createLegend(legend, true)

  function createLegend(legend, useDensity){
    // Legend for population density
    if (useDensity) {
      var div = document.createElement('center');
      div.innerHTML = '<h3>Relative<br/>Median<br/>Income<br/>(€)</center></h3>'
      legend.appendChild(div);
      for (var key in colors) {
        var color = colors[key];
        var label = labels[key];
        var div = document.createElement('div');
        div.innerHTML = '<div class="cbox" style="background-color: '+ color + '; padding: 5px; box-sizing: border-box; opacity: 0.8;"><center>'+label+'</center>';
        legend.appendChild(div);
      };
    // Legend for relative median income
    } else {
      var div = document.createElement('center');
      div.innerHTML = '<h3>Population<br/>density<br/>(persons / km<sup>2</sup>)</center></h3>'
      legend.appendChild(div);
      for (var key in bcolors) {
        var color = bcolors[key];
        var label = blabels[key];
        var div = document.createElement('div');
        div.innerHTML = '<div class="cbox" style="background-color: '+ color + '; padding: 5px; box-sizing: border-box; opacity: 0.8;"><center>'+label+'</center>';
        legend.appendChild(div);
      };
    };
  }
  map.controls[google.maps.ControlPosition.LEFT_BOTTOM].push(legend); 
```

# 最后一步:在 HTML 文件中嵌入 JS

我们已经定义了所有想要包含在基于谷歌地图的地理数据可视化工具中的 JavaScript 元素。创建一个完整的 Google Maps 网页的最后一步是将 JS 代码嵌入到 HTML 文件中，并加载 Google Maps JS API。我使用了 API 文档中的一个基本 HTML 模板来完成这个任务，您可以在下面找到它。请注意，我将 Google Maps API 键留空(`YOUR_API_KEY_HERE`)。因此，在浏览器中打开这个 HTML 文件不会创建工作地图，除非您填写自己的 API 键来激活 JS API。您可能已经注意到，API 键是通过 URL 字符串中的一个参数传递给 Google Maps API 初始化器的。

当我想在浏览器中加载地图时，我使用了这个简单的 [Python 脚本](https://github.com/nholmber/google-maps-statistics/blob/master/map.py)来设置 API 键并输出一个新的 HTML 文件。该脚本还定义了创建地图图例时使用的颜色和标注，这是我们在上一节中详细讨论过的问题。如果您在这个[文件](https://github.com/nholmber/google-maps-statistics/blob/master/key.py)中定义了 API 键，那么您可以使用相同的 Python 脚本来重新创建本文开头展示的地图。

```
<!DOCTYPE html>
<html>
  <head>
    Visualizing Demographic Data with Google Maps
    <meta name="viewport" content="initial-scale=1.0">
    <meta charset="utf-8">
    <style>
      /* Always set the map height explicitly to define the size of the div
       * element that contains the map. */
      #map {
        height: 100%;
      }
      #legend {
        font-family: Arial, sans-serif;
        background: #fff;
        padding: 10px;
        margin: 10px;
        border: 3px solid #000;
        border-collapse: collapse;
      }
      #legend h3 {
        margin-top: 0;
      }
      .cbox {
        font-size: 150%;
        font-weight: bold;
        color: #ffe680;
        text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
      }
      /* Optional: Makes the sample page fill the window. */
      html, body {
        height: 100%;
        margin: 0;
        padding: 0;
      }
    </style>
  </head>
  <body>
    <div id="map" ></div>
    <div id="legend"></div>
    <div id="color-definitions" style="display: none;">COLOR_DEFINITIONS</div>
    <div id="labels" style="display: none;">LABEL_DEFINITIONS</div>
    <div id="color-definitions-alternate" style="display: none;">COLOR_ALTERNATE_DEFINITIONS</div>
    <div id="labels-alternate" style="display: none;">LABEL_ALTERNATE_DEFINITIONS</div>
    <script src="map.js"></script>
    <script async defer
    src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY_HERE&callback=initMap">
    </script>
  </body>
</html> 
```

* * *

*这篇文章最初出现在我的[个人博客](https://nholmber.github.io/2018/08/gmaps-statistics/)上。*