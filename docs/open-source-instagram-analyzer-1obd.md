# 开源:Instagram Analyzer

> 原文：<https://dev.to/nejckorasa/open-source-instagram-analyzer-1obd>

[instagram-analyzer](https://github.com/nejckorasa/instagram-analyzer) 是一个用 Python 编写的应用程序，它使用用户 instagram 照片和视频中的反向地理编码来分析地理标签。

它提供了你目前为止访问过的具体地点、国家和城市的数据，以及有多少次和哪些 Instagram 帖子与该地点匹配。

我想听听反馈，好的或坏的，所以请去看看吧！
感谢

## 它有什么作用

### 📍存储所有 instagram 媒体数据📷

应用程序加载所有用户的 instagram 媒体，并以 JSON 格式保存。该数据包括所有媒体元数据，包括喜欢、位置、标记的用户、评论、图像 url...

### 📍存储所有 instagram 位置数据📊

分析地理标签并将位置保存在 JSON forma 中。这些数据包括每个地点的事件以及图片和 instagram 媒体 url...

### 📍存储所有 instagram 国家和城市的位置数据

国家和城市还通过使用 [LocationIQ API](https://locationiq.com) 进行反向地理编码进行分析。数据保存在 JSON 文件中。

### 📍打印✈️地点，国家和城市事件

```
You have visited 99 different locations
You have visited 7  different countries
You have visited 32 different cities 
```

Enter fullscreen mode Exit fullscreen mode

### 打印最常去的地点、国家和城市的表格视图🌍

例如，当执行 [nejckorasa](https://www.instagram.com/nejckorasa) 时，打印的国家是这样的:

```
Countries: 

+------+-----------------+-------------+
| rank | country         | occurrences |
+------+-----------------+-------------+
|  1   | Slovenia        |     51      |
+------+-----------------+-------------+
|  2   | The Netherlands |     12      |
+------+-----------------+-------------+
|  3   | Spain           |      8      |
+------+-----------------+-------------+
|  4   | Poland          |      8      |
+------+-----------------+-------------+
|  5   | Russia          |      7      |
+------+-----------------+-------------+
|  6   | Croatia         |      7      |
+------+-----------------+-------------+
|  7   | Hungary         |      6      |
+------+-----------------+-------------+ 
```

Enter fullscreen mode Exit fullscreen mode

为特定地点和城市打印类似的表。

## 安装

安装 instagram-analyzer:

```
$ pip install instagram-analyzer 
```

Enter fullscreen mode Exit fullscreen mode

更新 instagram-analyzer:

```
$ pip install instagram-analyzer --upgrade 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

安装后，导入、配置并运行:

```
from instagram_analyzer import InstaAnalyzer

InstaAnalyzer(
    insta_token='<INSTAGRAM_TOKEN_HERE>',
    location_iq_token='<LOCATION_IQ_TOKEN_HERE>').run() 
```

Enter fullscreen mode Exit fullscreen mode

在运行之前，请查看[配置&选项](https://github.com/nejckorasa/instagram-analyzer/blob/master/README.md#configuration--options)

## 配置&选项

### 获取代币

##### 获取 Instagram 访问令牌

去 [Pixelunion](http://instagram.pixelunion.net/) ，生成令牌，别忘了令牌！

##### 获取位置 IQ 访问令牌

去[地点智商](https://locationiq.com/)，报名，领令牌，别忘了令牌！

### 配置并运行

用标记值创建`InstaAnalyzer`实例。

```
analyzer = InstaAnalyzer(
    insta_token='<INSTAGRAM_TOKEN_HERE>',
    location_iq_token='<LOCATION_IQ_TOKEN_HERE>')
analyzer.read_media_from_file = False
analyzer.run() 
```

Enter fullscreen mode Exit fullscreen mode

> 一旦 instagram 媒体数据被存储在 JSON 中，你就可以从那里读取它，而不是通过 Instagram API (API 被限制为每小时 200 个请求)再次加载它。设置`analyzer.read_media_from_file = True`

### 选项

*   `location_iq_token`是可选的。如果未设置，将只运行基本位置分析并保存到文件中。
*   运行`InstaAnalyzer`后，所有数据均可访问:

```
# Configure InstaAnalyzer analyzer = InstaAnalyzer(
    insta_token='<INSTAGRAM_TOKEN_HERE>',
    location_iq_token='<LOCATION_IQ_TOKEN_HERE>')

# Run InstaAnalyzer analyzer.run()

# Access cities, countries and location data cities = analyzer.cities
countires = analyzer.countires
locations = analyzer.locations

# Access instagram media data instagram_media = analyzer.insta_media_data

# Print locations later analyzer.print_locations() 
```

Enter fullscreen mode Exit fullscreen mode

## 存储数据示例

当针对 [nejckorasa](https://www.instagram.com/nejckorasa) 执行时，一个国家项目(西班牙)的数据如下所示:

```
"Spain":  {  "count":  8,  "media_items":  [  [  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/e7705068da5e289f5e44c0c396c08f74/5BD54C95/t51.2885-15/sh0.08/e35/p640x640/36149213_609452269436842_8766778259800064000_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/Bkh3-KfgxL9/"  }  ],  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/2b239894a363f6bbe93d604ab2cdfa8a/5BE953CD/t51.2885-15/sh0.08/e35/p640x640/33941046_171665143683479_8766885676932136960_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/Bj7Uj56gxBs/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/9d7003f674af9ca05accf9961df893a6/5BE28FDA/t51.2885-15/sh0.08/e35/p640x640/33120615_197967877520708_8731075699906969600_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/Bjmp-6bAYus/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/1e7ca79fc44823ff3ef8b24e6dd55e61/5BD1E8C3/t51.2885-15/sh0.08/e35/p640x640/33608474_597094857325212_724188974242856960_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/BjR_9lpAqpc/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/1b046c05b1cbe9708f57f5e591b68d1c/5BD8E039/t51.2885-15/sh0.08/e35/p640x640/32947036_172314443452529_4611639929133334528_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/BjNEIwiA6Py/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/5ac0e05fb60700cba4c41d6d1216eb5b/5BC8A9DB/t51.2885-15/e15/10802615_318814311644936_1896556761_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/vdWuHBkwuY/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/40620d8f5e7e01a546e2b958d18bd42a/5BE9E99F/t51.2885-15/e15/10784835_319487204924131_388050040_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/vYybQyEwiA/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/b733c0bdf312ee5c21bb3fd6148e6221/5BE263EA/t51.2885-15/e15/10802986_691193854310946_2042620114_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/vc9ZFakwrq/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/875bff08c310444273eae90a67e525dd/5BC8F29F/t51.2885-15/e15/928044_671144066338855_1666493611_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/vaWbQLEwqX/"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，`<post_id>`将是一个实际的文章 ID。

城市的数据几乎相同。对于特定的位置，一个位置项如下所示:

```
"236678869":  {  "latitude":  45.7925,  "longitude":  15.1647,  "name":  "Novo Mesto",  "id":  236678869,  "count":  4,  "media_items":  [  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/6941d16b164ec488dd3a303004344f78/5BE40DE8/t51.2885-15/sh0.08/e35/p640x640/31270267_1592482480868234_8257495365851283456_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/Bij24yzAdHB/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/3189c0f2e5931f47b4506046ff26afff/5BDB6109/t51.2885-15/e15/10724200_1496985983889525_746072573_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/uDDPHekwtW/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/fbf31b5c410c9036ce43862012249d02/5BEC3F36/t51.2885-15/e15/10488704_250740985124191_1862853011_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/q94LWMkwlk/"  },  {  "id":  "<post_id>",  "image":  "https://scontent.cdninstagram.com/vp/27c6681709c7b71fc86d8477c11d2b88/5BCAD041/t51.2885-15/e15/10013254_641464529259998_1091484863_n.jpg?efg=eyJ1cmxnZW4iOiJ1cmxnZW5fZnJvbV9pZyJ9",  "link":  "https://www.instagram.com/p/mKDvsikwsC/"  }  ],  "city":  "Novo mesto",  "additional_data":  {  "place_id":  "113385772",  "licence":  "\u00a9 LocationIQ.org CC BY 4.0, Data \u00a9 OpenStreetMap contributors, ODbL 1.0",  "osm_type":  "way",  "osm_id":  "167321715",  "lat":  "45.7897769",  "lon":  "15.1680662",  "display_name":  "Krka, Novo mesto, Jugovzhodna Slovenija, 8000, Slovenia",  "address":  {  "suburb":  "Krka",  "town":  "Novo mesto",  "state_district":  "Jugovzhodna Slovenija",  "postcode":  "8000",  "country":  "Slovenia",  "country_code":  "si"  },  "boundingbox":  [  "45.7858017",  "45.7927137",  "15.1640388",  "15.1725268"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

注意`additional_data`字段，该数据是使用[位置 IQ API](https://locationiq.com) 填充的

## 常见问题

#### 为什么加载附加位置数据需要这么长时间？

对于反向地理编码，使用 Location IQ API。API si 免费版本限制为每秒 1 个请求。这就是为什么额外的数据加载需要花费`<different_location_count>`秒。

## 去看看，留下反馈🙏

这里有一个 github 的链接: [instagram-analyzer](https://github.com/nejckorasa/instagram-analyzer)