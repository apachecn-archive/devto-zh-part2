# 用 leav 和 Jupyter 笔记本可视化 Strava 活动

> 原文：<https://dev.to/josefrousek/visualizing-strava-activites-with-folium-and-jupyter-notebook-i4p>

几天前，我参加了我的第一场比赛！这是一项名为[的短铁人三项赛。我的朋友说服了我将近两年。游泳是 500 米，自行车是 34 公里，跑步是 4.5 公里。我不经常游泳或跑步，但我很确定我能应付。我对骑自行车很有信心，结果证明这是一个错误。总的来说，我对结果很满意。我不是最后一名。我对游泳和跑步很满意，但我可以在自行车上加把劲。结果是我有一些有趣的数据可以玩。](https://slapsky-triatlon.webnode.cz/)

[![Map of my ride](img/2f13b771f3baa386902aed352fcac581.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--loiYMQ1N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltvfi6g4gjx8h0y5m5ut.png)

*骑行部分的速度可视化。从深蓝色代表最慢到深红色代表最快。*

在探索数据时，我总是使用 Jupyter 笔记本和 Folium。follow 让您直接从 Python 生成`leaflet.js`地图。

从 Strava 获取数据非常简单。首先，您需要您的访问令牌。你可以在设置的[我的 API 应用](https://www.strava.com/settings/api)部分获取。

```
from stravalib.client import Client
client = Client(access_token = <STRAVA_ACCESS_TOKEN>)

types = ['time', 'distance', 'latlng', 'altitude', 'velocity_smooth', 'moving', 'grade_smooth']
streams = client.get_activity_streams(<ACTIVITY_ID>, types=types) 
```

Enter fullscreen mode Exit fullscreen mode

然后使用`streams['altitude'].data`获取流数据。为了使结果图看起来更平滑，我们需要将相似的速度数据点分组。这做得很好。

```
locations = streams['latlng'].data
velocities = streams['velocity_smooth'].data

data = zip(locations, velocities)

groups = []
last_velocity = None
for location, velocity in data:
    if not last_velocity or abs(abs(last_velocity) - abs(velocity)) > 0.4:
        groups.append({'velocity': velocity, 'velocities': [], 'locations': []})
    groups[-1:][0]['locations'].append(location)
    groups[-1:][0]['velocities'].append(velocity)
    last_velocity = velocity

import statistics

for group in groups:
    group['velocity'] = statistics.median_high(group['velocities']) 
```

Enter fullscreen mode Exit fullscreen mode

为了正确地给线段着色，我们需要将值标准化并映射到颜色。幸运的是`matplotlib`除了将颜色转换为十六进制之外，还为我们做了这件事。首先，我们将使用`Normalize`来归一化该值，然后使用[色图](https://matplotlib.org/tutorials/colors/colormaps.html)值来着色。最后一步是将 rgb 0.0 - 1.0 转换为十六进制。

```
def convert_to_hex(rgba_color):
    red = str(hex(int(rgba_color[0]*255)))[2:].capitalize()
    green = str(hex(int(rgba_color[1]*255)))[2:].capitalize()
    blue = str(hex(int(rgba_color[2]*255)))[2:].capitalize()

    if blue=='0':
        blue = '00'
    if red=='0':
        red = '00'
    if green=='0':
        green='00'

    return '#'+ red + green + blue

import matplotlib.cm as cm
from matplotlib.colors import Normalize

cmap = cm.bwr
norm = Normalize(vmin=min(velocities), vmax=max(velocities))

def colorize(grade):
    return convert_to_hex(cmap(norm(grade))) 
```

Enter fullscreen mode Exit fullscreen mode

有了分组数据和正确的颜色，我们唯一需要做的就是为每个组生成`PolyLine`并显示地图。

```
import folium

m = folium.Map(location=[49.7947117,14.3916288], tiles='Stamen Toner', zoom_start=12.2)

for group in groups:
    folium.PolyLine(
        group['locations'],
        weight=10,
        color=colorize(group['velocity'])
    ).add_to(m)

m 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 nbviewer.jupyter.org 上看到整个笔记本。如果你想探索我在斯特拉发的活动，这里有我的[骑行](https://www.strava.com/activities/1732801109)和[跑步](https://www.strava.com/activities/1732871895)。