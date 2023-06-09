# 使用 GeoDjango 和 PostGIS 在区域内搜索

> 原文：<https://dev.to/adamghill/searching-within-an-area-with-geodjango-and-postgis-24g8>

[InHerSight.com](https://www.inhersight.com)的一个已经被证明非常有用的特性是，当[探索潜在公司](https://www.inhersight.com/discover)时，能够使用位置作为过滤器。幸运的是，我们正在使用 [Django](https://www.djangoproject.com/) 和 [PostgreSQL](https://www.postgresql.org) ，它们对使用 [GeoDjango](https://docs.djangoproject.com/en/1.9/ref/contrib/gis/) 和 [PostGIS](http://postgis.net/) 的地理查询提供了强大的支持。我还使用了令人惊叹的 django-cities 包来获取大量与地点相关的数据。

在 StackOverflow 上或者通过我的搜索，没有很多很好的文档，所以我做了很多尝试和错误，以得到一个足够好的解决方案。

初始代码的简化版本如下所示:

```
# models.py from django.contrib.contenttypes.fields import GenericRelation
from django.contrib.gis.db.models import PointField
from cities.models import City

class Company(models.Model):
    name = models.CharField()
    places = GenericRelation(Place)

class Place(models.Model):
    point = PointField()
    city = models.ForeignKey(City)

# views.py from django.db.models import F
from django.contrib.gis.db.models import PointField
from cities.models import City

distance_miles_to_search = 50
point = PointField()  # get a point of a particular place (usually by latitude/longitude) companies = Company.objects.filter(places__point__distance_lte=(point, D(mi=distance_miles_to_search))).annotate(closest_city_id=F('places__city')) 
```

Enter fullscreen mode Exit fullscreen mode

在最初的 MVP 中,`distance_lte`过滤器无疑是有效的，然而，随着我们继续添加公司和地点，我们开始注意到在按地点搜索公司时出现了一些阻塞。然后，当我们遇到突发流量时，速度变慢变得更加明显。New Relic 和 Heroku 的指标面板非常有助于确定哪里出现了减速。然而，即使经过多轮优化，InHerSight 在试图从现有架构中挤出更多性能时仍然碰壁了。

我想我可以通过在两个数据库之间分配读取来为主数据库设置一个只读副本，从而减少阻塞。这确实有所帮助，但是即使在最小负载的情况下，基于距离的查询仍然需要大约 2500 毫秒——远远高于可接受的阈值。

在 PostGIS 中，`distance_lte`过滤器转化为 [ST_Distance](http://postgis.net/docs/ST_Distance.html) 函数。我在不同的地方读到过(具体来说，http://stack overflow . com/questions/7845133/how-can-query-all-my-data-within-a-distance-of-5 米和[http://stack overflow . com/questions/2235043/GeoDjango-difference-between-DWI thin-and-distance-lt](http://stackoverflow.com/questions/2235043/geodjango-difference-between-dwithin-and-distance-lt))在 geojango 中也有`dwithin`过滤器子句，但我从来没有读到过`dwithin`翻译成 PostGIS 中的 [ST_DWithin](http://postgis.net/docs/ST_DWithin.html) 函数，并使用一个空间索引，该索引[应该会带来更快的性能](http://stackoverflow.com/questions/7845133/how-can-i-query-all-my-data-within-a-distance-of-5-meters#comment9588147_7845663)。

我的代码的简化版本使用了`dwithin`过滤子句:

```
# views.py from django.contrib.gis.measure import D
from django.db.models import F
from cities.models import City

def convert_miles_to_degrees(miles):
    '''
    Convert miles to degrees. Somewhat imprecise, but probably good enough.
    '''

    meters = D(mi=miles).m
    degrees = (meters / 40000000 * 360)

    return degrees

degrees = convert_miles_to_degrees(50)
point = City.objects.get(pk=...).location
companies = Company.objects.filter(places__point__dwithin=(point, degrees))\
                .annotate(closest_city_id=F('places__city')) 
```

Enter fullscreen mode Exit fullscreen mode

在我的本地开发机器上使用`ST_DWithin`将位置查询提高了大约 30 倍，在实时数据库上提高了 10 倍。即使有负载，该查询现在也徘徊在 200 毫秒左右。当我可以的时候，我会非常积极地缓存数据，将来可能会有一些机会预先计算一些典型的搜索或使用像 ElasticSearch 或 Lucene 这样的东西，但只是从`ST_Distance`切换到`ST_DWithin`就大大提高了基于位置的查询性能。

*最初发表于[adamghill.com](https://adamghill.com/)。*