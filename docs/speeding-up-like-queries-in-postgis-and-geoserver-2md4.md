# 加速类似 PostGIS(和 GeoServer)中的查询

> 原文：<https://dev.to/ianturton/speeding-up-like-queries-in-postgis-and-geoserver-2md4>

我经常在 PostGIS 和 GeoServer 中使用`like`查询到 PostGIS 数据存储中。但最近，一名学员在课程中询问他们是否足够快，允许他们在 CQL 查询中使用，以允许最终用户动态生成新图层。

经过一些谷歌搜索，我们发现他们可能是，但有一些技巧，你需要使用，以获得索引的好处。

我总是索引我打算在层的样式中使用的或者将在请求中查询的任何属性(列)。但事实证明，如果您正在进行`like`查询，这可能还不够好。

为了测试这一点，我使用了英国国家测绘局的公开数据[矢量地图区](https://www.ordnancesurvey.co.uk/business-and-government/products/vectormap-district.html)数据集，它覆盖了整个英国，很大但并不巨大。例如，roads 表有 280 万个路段，其中 80 万个路段已命名。这里是“默认”索引。

```
CREATE INDEX idx_distinictive_name
  ON vmd.road
  USING btree
  (distinctivename COLLATE pg_catalog."default"); 
```

因此，我运行了一个典型的查询来查找有多少条道路是以某种橡树命名的:

```
select distinctivename, count(ogc_fid) 
from vmd.road 
where distinctivename
like '%oak%' 
group by distinctivename order by count desc; 
```

在我的台式机上，运行这个程序需要 162 毫秒，这还不算太糟，但可能会更好。如果我们看一下查询计划:

```
Sort (cost=83988.80..83988.97 rows=67 width=21)
  Sort Key: (count(ogc_fid)) DESC
  -> Finalize GroupAggregate (cost=83978.87..83986.77 rows=67 width=21)
      Group Key: distinctivename
      -> Gather Merge (cost=83978.87..83985.82 rows=56 width=21)
          Workers Planned: 2
          -> Partial GroupAggregate (cost=82978.84..82979.33 rows=28 width=21)
              Group Key: distinctivename
              -> Sort (cost=82978.84..82978.91 rows=28 width=17)
                    Sort Key: distinctivename
                    -> Parallel Seq Scan on road (cost=0.00..82978.17 rows=28 width=17)
                          Filter: ((distinctivename)::text ~~'%oak%'::text) 
```

没有提到指数！哇！我以前从来没有真正检查过这个，所以这有点令人震惊。因此，经过一番战略性的谷歌搜索，我找到了这个 [stackoverflow 问答](https://stackoverflow.com/questions/1566717/postgresql-like-query-performance-variations)，它建议我添加`pg_trgm`扩展，这样我就有了支持所有`like`和`ilike`模式的 GIN 和 GiST 三元组索引。

```
CREATE EXTENSION pg_trgm

CREATE INDEX idx_distinictive_name2
  ON vmd.road
    USING gin
      (distinctivename COLLATE pg_catalog."default" gin_trgm_ops); 
```

现在，查询只需要 20 毫秒，查询计划看起来更好，因为前面和中间使用了一个索引:

```
Sort (cost=284.30..284.47 rows=67 width=21)
  Sort Key: (count(ogc_fid)) DESC
  -> GroupAggregate (cost=281.10..282.27 rows=67 width=21)
        Group Key: distinctivename
        -> Sort (cost=281.10..281.26 rows=67 width=17)
              Sort Key: distinctivename
              -> Bitmap Heap Scan on road (cost=16.52..279.07 rows=67 width=17)
                    Recheck Cond: ((distinctivename)::text ~~'%oak%'::text)
                    -> Bitmap Index Scan on idx_distinictive_name2 (cost=0.00..16.50 rows=67 width=0)
                          Index Cond: ((distinctivename)::text ~~'%oak%'::text) 
```

当您使用 PostGIS 数据存储时，所有这些加速都会直接传递到 GeoServer，因为这些查询会传递到数据库进行处理。因此，确保您不仅索引了一个属性，而且对您期望看到的查询类型使用了正确的索引是值得的。

对于使用`ILIKE`的较大的[矢量地图本地](https://www.ordnancesurvey.co.uk/business-and-government/products/vectormap-local.html)数据，由于地形测量使用所有大写字母作为道路名称，因此速度从 760 毫秒提高到 122 毫秒。