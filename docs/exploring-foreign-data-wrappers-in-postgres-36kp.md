# 探索 Postgres 中的外部数据包装器

> 原文：<https://dev.to/citizen428/exploring-foreign-data-wrappers-in-postgres-36kp>

多年来，Postgres 一直是我的首选数据库，但我仍然会定期发现一些我以前不知道的有趣的新功能。这个长长的列表中最新的条目是"[外部数据包装器](https://www.postgresql.org/docs/10/static/postgres-fdw.html)"，这是 Postgres 9.1 中添加的 [SQL/MED](https://en.wikipedia.org/wiki/SQL/MED) (“外部数据的 SQL 管理”)规范的实现。

这种机制允许将我们的数据库与存储在数据库之外的数据集成在一起。最常见的用例可能是访问远程 RDBMS (Postgres 或其他)，但是在这个例子中，我们将使用[文件远程数据包装器](https://www.postgresql.org/docs/10/static/file-fdw.html)从磁盘访问 CSV 文件。

### 整合外部数据

在这个例子中，我们将使用来自 [Simplemaps](https://simplemaps.com) 的[基本世界城市数据集](https://simplemaps.com/static/data/world-cities/basic/simplemaps-worldcities-basic.csv)，所以如果你想继续，现在就去下载这个文件吧。

为了能够定义一个“外部表”，我们首先需要安装`file_fdw`扩展并定义一个使用它的服务器。

```
CREATE EXTENSION file_fdw;
CREATE SERVER cities FOREIGN DATA WRAPPER file_fdw; 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了自己的服务器，我们就可以定义一个外部表来访问其中的数据:

```
CREATE FOREIGN TABLE cities (
  city text,
  city_ascii text,
  lat real,
  long real,
  population real,
  country text,
  iso2 text,
  iso3 text,
  province text
) SERVER cities
OPTIONS ( 
  filename '/tmp/simplemaps-worldcities-basic.csv', 
  format 'csv', 
  header 'true'
); 
```

Enter fullscreen mode Exit fullscreen mode

上面创建了我们的表的模式，还指定了我们的数据文件的位置，文件格式(`file_fdw`不仅仅支持 CSV)并指示 Postgres 忽略文档的第一行，因为它是一个头。

我们现在可以像查询任何常规旧表一样查询这个 CSV 文件:

```
test=# select city, population::int from cities order by population desc limit 3;
    city     | population
-------------+------------
 Tokyo       |   22006300
 Mumbai      |   15834918
 Mexico City |   14919501
(3 rows) 
```

Enter fullscreen mode Exit fullscreen mode

我不知道你怎么想，但是我发现不需要先编写导入程序就可以访问 CSV 数据的能力非常令人兴奋，性能也相当不错:

```
test=# explain analyze select avg(population) from cities;
                                                     QUERY PLAN
---------------------------------------------------------------------------------------------------------------------
 Aggregate  (cost=1896.14..1896.15 rows=1 width=8) (actual time=12.437..12.437 rows=1 loops=1)
   ->  Foreign Scan on cities  (cost=0.00..1851.60 rows=17816 width=4) (actual time=0.064..10.648 rows=7322 loops=1)
         Foreign File: /tmp/simplemaps-worldcities-basic.csv
         Foreign File Size: 570124
 Planning time: 0.605 ms
 Execution time: 13.517 ms
(6 rows) 
```

Enter fullscreen mode Exit fullscreen mode

### 进入续集

现在是时候从 Ruby 访问我们的外部表了。为此，我们将使用杰雷米·埃文斯的优秀[续集](https://github.com/jeremyevans/sequel)宝石。它有自己的控制台，所以命令`sequel postgres://localhost/test`会让我们进入一个 IRB 会话，其中已经需要 gem，并且`Sequel::Database`对象存储在常量`DB` :
中

```
→ sequel postgres://localhost/test
Your database is stored in DB...
>> DB
#=> #<Sequel::Postgres::Database: "postgres://localhost/test" {:test=>true, :loggers=>[]}> 
```

Enter fullscreen mode Exit fullscreen mode

第一步，我们将`cities`表赋给一个变量。这将返回一个 [Sequel::Dataset](https://sequel.jeremyevans.net/rdoc/files/doc/dataset_basics_rdoc.html) ，它表示当我们想要检索数据时将执行的查询。

```
cities = DB[:cities]
#=> #<Sequel::Postgres::Dataset: "SELECT * FROM \"cities\""> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以定义一个名为`top3`的数据集，它非常类似于上面显示的 SQL 查询。

```
top3 = cities.select(:city, :population).reverse_order(:population).limit(3)
#=> #<Sequel::Postgres::Dataset: "SELECT \"city\", \"population\" FROM \"cities\" ORDER BY \"population\" DESC LIMIT 3"> 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果我们启用了 Sequel 的[核心扩展](http://sequel.jeremyevans.net/rdoc/files/doc/core_extensions_rdoc.html)，那么`order`子句可能会被写成`order(:population.desc)`，但是人们是否想要使用这种风格取决于个人喜好。

所有的`Sequel::Datasets`都包含了`Enumerable`模块，所以我们可以使用它的`#to_a`方法来物化我们的数据集:

```
top3.to_a
#=> [{:city=>"Tokyo", :population=>22006300.0}, {:city=>"Mumbai", :population=>15834900.0}, {:city=>"Mexico City", :population=>14919500.0}] 
```

Enter fullscreen mode Exit fullscreen mode

Ruby 和 Sequel 很幸运地不知道他们正在处理一个外来表，而我们正在执行从 Postgres 控制台发出的相同查询。

### 但是我要对象！

虽然上面的查询有效，但它返回了一个普通散列数组。如果你喜欢对象，最好使用 [Sequel::Model](http://sequel.jeremyevans.net/rdoc/classes/Sequel/Model.html) 来代替。为此，我们需要这样定义一个类:

```
class City < Sequel::Model ; end 
```

Enter fullscreen mode Exit fullscreen mode

这通过一个类方法公开了底层数据集:

```
City.dataset
=> #<Sequel::Postgres::Dataset: "SELECT * FROM \"cities\""> 
```

Enter fullscreen mode Exit fullscreen mode

然而，将方法直接链接到其上并不是好的风格。相反，文档[推荐](http://sequel.jeremyevans.net/rdoc/files/doc/model_dataset_method_design_rdoc.html#label-Flexibility-3A+Use+Single+Method+Per+Task)对每个任务使用单一的方法。如果您对这里的`dataset_module`感到疑惑，这是一个方便的方法，让我们创建返回缓存数据集的命名数据集方法:

```
class City < Sequel::Model
  dataset_module do
    def top3
      select(:city, :population).
      reverse_order(:population).
      limit(3)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用我们新定义的类:

```
top3 = City.top3.to_a
#=> [#<City @values={:city=>"Tokyo", :population=>22006300.0}>, #<City @values={:city=>"Mumbai", :population=>15834900.0}>, #<City @values={:city=>"Mexico City", :population=>14919500.0}>]

top3.first.city
#=> "Tokyo" 
```

Enter fullscreen mode Exit fullscreen mode

结果和以前一样，只是用对象访问代替了普通散列。

### 总结起来

Postgres 的外来数据包装器是一个有趣的概念，我肯定会花更多的时间来探索它们，尤其是由 [Multicorn 项目](https://multicorn.org)提供的更奇特的概念，如 LDAP、IMAP 和 RSS。

Sequel 是一个很棒的宝石，也很好地提醒了我们，当涉及到 Ruby 中的数据库访问和 ORM 时,`ActiveRecord`远不是唯一的游戏。