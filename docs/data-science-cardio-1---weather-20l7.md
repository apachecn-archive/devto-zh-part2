# 数据科学有氧运动 1 -天气

> 原文：<https://dev.to/rpalo/data-science-cardio-1---weather-20l7>

我将无耻地借用韦斯·博斯的 JavaScript30 课程中的编程理念。我想你们可能会喜欢我提出一个简短的数据科学示例问题，然后与你一起解决它。我现在正在辅导一个学习 Python 的学生(我的第一个学生！耶！)，这是她的问题之一。这似乎是一个涵盖了很多基础的例子。这个例子将在 Python (3)中。当它们出现时，我会尝试链接到适当的库和文档，这样你就可以进一步探索，而不是相信我的话。

那么，事不宜迟，让我们。去拿。抽水！

## 0。问题是

我们被要求调查各种天气现象是如何随纬度变化的。具体来说，我们需要收集至少 500 个随机分布在全球各地的天气数据样本。一旦我们有了这些数据，我们应该绘制图表，并对我们在温度、湿度、云量和风速方面看到的任何模式进行评论。我要转换成美国惯用的单位制。你做任何让你开心的事。

注意:有很多不同的方法可以解决这个问题。我将向你展示一种方法。请随意探索您自己的解决方法，看看结果是否相似。

第二点注意:我使用了一些不属于标准库的库，但是可以在 Python 包索引(PyPI)中找到。如果你遇到一个`No module named 'whatever'`错误，你需要打开一个终端窗口，输入`pip install <packagename>`，其中`<packagename>`是你丢失的包的名字，然后回车。或者，如果您使用 Jupyter 笔记本，您也可以在单元格中键入`! pip install <packagename>`并运行它。bang ( `!`)让笔记本运行一行系统调用。

我最初用一个 [Jupyter 笔记本](http://jupyter.org/install)完成了这个分析。我强烈推荐。你可以在这里找到[源代码回购](https://github.com/rpalo/tutoring-weather-example)，如果你坐立不安，想提前一窥。

## 1。500 个随机坐标

我们首先需要 500 个随机坐标。我们需要这些数字跨越整个可能的纬度范围(T1)(-90 度到 90 度)，以及整个可能的经度范围(T3)(-180 度到 180 度)。注意，负纬度表示南方，负经度表示西方。

```
import numpy as np
import pandas as pd

np.random.seed(125)  # So that other scientists can duplicate our work! lats = np.random.randint(-90, 90, size=500)
longs = np.random.randint(-180, 180, size=500)
coords = pd.DataFrame({
    "latitude": lats,
    "longitude": longs
})

# Let's take a look at how our coordinates look coords.head() 
```

Enter fullscreen mode Exit fullscreen mode

|  | 纬度 | 经度 |
| --- | --- | --- |
| Zero | Sixty-seven | -117 |
| one | -3 | Eleven |
| Two | -23 | -146 |
| three | Twenty | -19 |
| four | -47 | six |

为了理智起见，让我们确保我们的坐标是合理随机的。

```
from matplotlib import pyplot as plt
# And, we're going to give our plots a bit of pizazz.
# Feel free to skip these two lines import seaborn

seaborn.set()

plt.hist(coords['latitude'])
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Latitude histogram](img/e023a28a0ab479bf7d8bcc8ae2e455bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UHKwbOxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xg74gpmtk0sb7im7pwkm.png)T3】

```
plt.hist(coords['longitude'])
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Longitude histogram](img/6c6fa575da042f42113e78f8b93a7642.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IKq6_zjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0907t6sn2r99pzz69oot.png)

有一些尖峰，但总的来说，我们所做的似乎是合理的。如果你对数据的随机性不满意，继续改变随机种子并重新运行上面的单元格。

## 2。设置天气 API

这部分将会是更多的管理和更少乐趣的编程。不过没关系！为了获得这些天气数据，我们需要访问一个 web API 并向它请求数据。具体来说，我们将使用 [OpenWeatherData API](https://openweathermap.org/) 。您需要创建一个帐户(这是免费的！)并且您将获得一个 API 密钥，您可以在您的帐户页面的“API 密钥”选项卡上找到它。

[![The API tab on the account page](img/c1aa62d60afd9a6710b857d6fa964137.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wi4zgUpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jzorxlq4fl43jku94el6.png)

保持这个密钥的秘密(我会给你一些关于如何做好这件事的提示)。你不会希望某个邪恶的人篡改天气预报，让所有人都以为是你。想想你作为一个好天气 API 公民的声誉！想想孩子们！

正如页面所说，你的钥匙可能需要一段时间才能工作。幸运的是，在我们准备使用它之前，我们已经做了一些设置。现在，让我们看看我们将使用的端点。查看[坐标天气终点文档](https://openweathermap.org/current#geo)。

我们可以通过几种不同的方式来获取我们需要的数据，但是由于我们已经创建了一堆漂亮的(纬度、经度)对，我认为这可能是最简单的方法。

```
http://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&APPID={api_key} 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，即使在线文档没有讨论这个问题，我们也需要添加带有 API 键的参数`APPID`。如果你感觉非常凉爽，你还可以添加`units=imperial`来获得华氏温度和英里/小时风速。您也可以坚持默认设置，以后再进行转换。我也将向您展示这个过程。现在，足够的行政事务！让我们回到代码上来！

## 3。设置以获取数据

在我们打开分析代码之前，我建议您在同一个目录中打开一个名为`secrets.py`的新文件。

```
# secrets.py API_KEY = "copy your api key here" 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 git 存储库跟踪这个项目，那么将这个文件添加到您的`.gitignore`文件中。

```
__pycache__/
.ipynb_checkpoints
secrets.py
haters 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好回到笔记本中。

```
from secrets import API_KEY
import requests
import time

def get_weather_data(coords, time_between=1):
    """Queries the OpenWeatherAPI for data.

    Args:
        coords: A Pandas DataFrame with rows containing 'latitude'
            and 'longitude' columns.
        time_between: An integer specifying the sleep time in seconds
            between each API ping.  Defaults to the OpenWeatherAPI's
            recommended limit of 1 request per second.

    Returns:
        A list of nested dicts (loaded JSON results).
    """
    results = []
    for ind, row in coords.iterrows():
        lat, lon = row['latitude'], row['longitude']
        query = f"http://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&APPID={API_KEY}"
        response = requests.get(query)
        results.append(response.json())
        time.sleep(time_between)
    return results 
```

Enter fullscreen mode Exit fullscreen mode

该代码有两个关键特征。第一个是“f-string”，这是 Python 3 中用于字符串插值的 shwoopy 语法。好的一面是这些“f 弦”超级快！相对来说，至少。但是我们能够直接从 DataFrame 行插入我们的纬度和经度值，以及我们的 API 键。

另一个关键特性是，我们使用 [`requests`](http://docs.python-requests.org/en/master/) 到[发出一个`get`请求](http://docs.python-requests.org/en/master/user/quickstart/#make-a-request)，然后[使用`json`函数](http://docs.python-requests.org/en/master/user/quickstart/#json-response-content)立即将响应处理成我们可以使用的 Python dict。如果您不确定我们将如何从 API 中获取数据，您可能会失望，因为它并不比这更复杂。只要你知道正确的网址，`requests`会让我们的工作变得非常愉快。

## 3a。记录我们的请求

我要去做两次短暂的休息，进行一些额外的练习。如果你想直接跳到第四步，不用担心。你不会伤害我的感情。

我要讲的第一个题外话是为文件设置一些日志记录。在笔记本的顶部，添加以下代码。

```
import logging

logger = logging.getLogger('weather')
logger.setLevel(logging.INFO)
fh = logging.FileHandler('api_calls.log')
formatter = logging.Formatter('%(asctime)s - %(message)')
fh.setFormatter(formatter)
logger.addHandler(fh) 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的`get_weather_data`函数中:

```
def get_weather_data(coords, time_between=1):
    # ...
    results = []
    for ind, row in coords.iterrows():
        lat, long = row['latitude'], row['longitude']
        query = f"http://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&APPID={API_KEY}"

        # Here's the new stuff
        clean_url = query.rpartition("&")[0]  # Don't log your api key!
        logger.info(f"Call {ind}: ({lat}, {lon}) - {clean_url}")

        response = requests.get(query)
        results.append(response.json())
        time.sleep(time_between)
    return results 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以保存所有我们点击的 URL 的日志了！

## 3b。获取最近的城市名称

你知道什么会更好吗？用我们的日志记录下最近的城市的名字。有一个叫做 [`citipy`](https://github.com/wingchen/citipy) 的整洁的小库就是这么做的！让我们再更新一次我们的`get_weather_data`函数。

```
from secrets import API_KEY
from citipy import citipy  # Make sure to import it once you've installed it 
def get_weather_data(coords, time_between=1):
    # ...
    results = []
    for ind, row in coords.iterrows():
        lat, lon = row['latitude'], row['longitude']
        query = f"http://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&APPID={API_KEY}"
        clean_url = query.rpartition("&")[0]

        # Here's the new stuff
        city = citipy.nearest_city(lat, lon)
        logger.info(f"Call {ind}: {city.city_name}  {clean_url})")

        result = requests.get(query)
        results.append(result.json())
        time.sleep(time_between)
    return results 
```

Enter fullscreen mode Exit fullscreen mode

这会很棒的！回到手头的问题。

## 4。实际获取我们的数据

首先，让我们用一个测试调用来测试我们的函数。

```
test_coords = pd.DataFrame({"latitude": [37], "longitude": [-122]})
test_results = get_weather_data(test_coords)
test_results 
```

Enter fullscreen mode Exit fullscreen mode

```
[{'base':  'stations',  'clouds':  {'all':  1},  'cod':  200,  'coord':  {'lat':  37,  'lon':  -122},  'dt':  1522341300,  'id':  5381421,  'main':  {'humidity':  76,  'pressure':  1021,  'temp':  287.78,  'temp_max':  289.15,  'temp_min':  286.15},  'name':  'Pasatiempo',  'sys':  {'country':  'US',  'id':  399,  'message':  0.004,  'sunrise':  1522331815,  'sunset':  1522376913,  'type':  1},  'visibility':  16093,  'weather':  [{'description':  'clear  sky',  'icon':  '01d',  'id':  800,  'main':  'Clear'}],  'wind':  {'deg':  331.003,  'speed':  1.32}}] 
```

Enter fullscreen mode Exit fullscreen mode

如果你的结果和我的一样，那么看起来我们可以进行完整的数据收集了。

```
full_results = get_weather_data(coords)
full_results[:3]  # Let's peek at the first 3 datapoints 
```

Enter fullscreen mode Exit fullscreen mode

这将持续大约 8 分半钟(做一个好公民的代价)。去喝杯咖啡或吃点零食，犒劳一下自己的辛勤工作。

## 5。保存数据

最重要的是。让我们把数据保存下来，这样万一有什么东西爆炸了，我们就有了。

```
import json

with open("weather.json", "w") as outfile:
    json.dump(full_results, outfile) 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的项目目录中创建一个新文件`weather.json`。是时候进行另一个可选的辅助步骤了:单位转换。

## 5a。单位换算

如果您在 API 调用中没有使用`units=imperial`参数，并且您希望我们使用惯用单位，那么您将需要一些帮助函数。

```
def k_to_f(temp):
    """Converts a Kelvin temperature to Fahrenheit"""
    return temp * 9/5 - 459.67

def mps_to_mph(speed):
    """Converts a meters/s speed to miles/hour"""
    return speed * 2.23694 
```

Enter fullscreen mode Exit fullscreen mode

## 6。管理数据

是的，是一个词。查一查。随便。我们将需要建立一个数据结构，我们可以把它变成一个数据框架，我们想把事情缩小到我们关心的数据。再看一下上面的示例输出，深入研究 JSON 数据。

```
important_json_data = []
for point in full_results:
    lat = point['coord']['lat']
    lon = point['coord']['lon']
    temp = k_to_f(point['main']['temp'])
    humidity = point['main']['humidity']
    cloudiness = point['clouds']['all']
    wind = mps_to_mph(point['wind']['speed'])

    row = [lat, lon, temp, humidity, cloudiness, wind]
    important_json_data.append(row)

weather_df = pd.DataFrame(important_json_data)
weather_df.columns = [
    "latitude",
    "longitude",
    "temperature",
    "humidity",
    "clouds",
    "wind",
]
weather_df.head() 
```

Enter fullscreen mode Exit fullscreen mode

|  | 纬度 | 经度 | 温度 | 湿度 | 云 | 风 |
| --- | --- | --- | --- | --- | --- | --- |
| Zero | Sixty-seven | -117 | -16.15 | sixty-nine | eight | Four point two nine |
| one | -3 | Eleven | Seventy-four point nine three | Ninety-six | sixty-eight | Two point one seven |
| Two | -23 | -146 | Eighty point nine six | One hundred | Eighty-eight | Twelve point nine one |
| three | Twenty | -19 | Sixty-seven point three seven | One hundred | eight | Nine point seven eight |
| four | -47 | six | Forty-four point seven eight | Ninety-seven | eight | Thirteen point three five |

同样，让我们保存我们的数据以防万一。

```
weather_df.to_csv("weather.csv") 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！繁重的工作完成了。让我们看看我们的数据，看看我们能得出什么结论。

## 7。绘制数据

记得我们的目标吗？

1.  比较温度和纬度。
2.  比较湿度和纬度。
3.  比较云量和纬度。
4.  比较风速和纬度。
5.  得出一些结论。

我将把纬度放在 Y 轴上，因为我觉得这些图会更直观。我们通常认为纬度从北到南，因此从上到下。如果你想坚持把自变量(纬度)标绘在 X 轴上，因变量(温度)标绘在 Y 轴上，那就做任何让你开心的事。

### 温度

```
plt.scatter(weather_df.temperature, weather_df.latitude)
plt.xlabel("Temperature (F)")
plt.ylabel("Latitude (degrees)")
plt.title("Temperature vs. Latitude")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Temperature Latitude plot](img/b63173bbb4255a15862bf9be929ed9af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--alHOuKzE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bgtwmzsrpwjxiss009ld.png)

呜哇！这是很强的趋势！正如你所料，当你接近赤道时，温度会上升，当你接近两极时，温度会下降。去科学吧！

### 湿度

```
plt.scatter(weather_df.humidity, weather_df.latitude)
plt.xlabel("Humidity (%)")
plt.ylabel("Latitude (degrees)")
plt.title("Humidity vs. Latitude")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Humidity vs. Latitude plot](img/4be96bbdab92842e693d0742f0b4788c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XopKXRzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lwuwigl92tiqw28cw99.png)

这些是一些奇怪的结果。看起来，除了一些下降，大量的数据点都是 100%的湿度。我觉得这很难相信。我发现了一些谷歌结果，这让我想知道他们测量湿度的方式是否有些奇怪。如果有人有其他想法，我很乐意听听。让我知道你的想法。

### 多云

```
plt.scatter(weather_df.clouds, weather_df.lat)
plt.xlabel("Cloudiness (%)")
plt.ylabel("Latitude (degrees)")
plt.title("Cloudiness vs. Latitude")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Cloudiness vs. Latitude plot](img/1e8dddf7c2edb04533a71cfe8fda0f18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q7Lnusqx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g9c3y70mcfrxb9a57jju.png)

我也看不出这有什么趋势。不过，数据的条纹(整齐的行)让我觉得有某种模式。让我们看看是否有经度的关系。

```
plt.scatter(weather_df.long, weather_df.clouds)
plt.xlabel("Longitude (degrees)")
plt.ylabel("Cloudiness (%)")
plt.title("Longitude vs. Cloudiness")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Longitude Cloudiness plot](img/3a4d819b87ab5a9ac5ff83d1e55c9915.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0_B2uv_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wgou728cv5iwba6jsvm5.png)

嗯（表示踌躇等）...我还是看不出有什么关系。再说一次，如果任何人有任何想法，让我知道！

### 风速

```
plt.scatter(weather_df.wind, weather_df.lat)
plt.xlabel("Wind Speed (mph (abs))")
plt.ylabel("Latitude (degrees)")
plt.title("Wind Speed vs. Latitude")
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![Wind Latitude plot](img/411633590e2bf8fecff63666421090f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3cbpa989--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oscs4zfleixzjvuifuje.png)

这是一个有趣的情节。我们看到的是一种混乱，但在-50 度和 50 度左右有一些明显的尖峰。在两极和赤道附近，它似乎向零下降。起初，我很困惑，但后来我想起了我 8 年级的科学课。

[![Global wind current patterns](img/6af03ea183e3e16427a415ec0e134bf4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xbq8GNuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ec192xfz2pafwb9yagj.png)

有一组称为“T0”西风带的风，在南北纬 40 度到 50 度之间吹拂。这些有时被称为“[咆哮四十度](https://en.wikipedia.org/wiki/Roaring_Forties)”，特别是由于南半球广阔的海洋(没有陆地或树木阻碍风)，它们被用来加快航行时间。在该半球的夏季，它们倾向于向赤道移动，而在冬季则向极地移动。

反过来，赤道周围的区域被称为“[热带辐合带，](https://en.wikipedia.org/wiki/Intertropical_Convergence_Zone)也称为“[无风带。](https://en.wikipedia.org/wiki/Doldrums)“这个地区是死风和雷暴的结合，取决于季节。

我有理由放心地说，我们的数据似乎支持这一趋势。所以，再一次，科学万岁！

## 总结起来

就是这样！希望你喜欢这个练习。如果你从我们的数据中发现了任何其他有趣的发现，一定要和我分享。

快乐 munging！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/03/28/data-science-cardio-weather/)*