# sequal para activerecord 重度用户

> 原文：<https://dev.to/debborafernandess/sequel-para-activerecord-heavy-users-5a3d>

*我只用我*中的主动记录

*我在简单的开发功能上遇到了问题，这些功能由于查询/写入表*而在生产上产生了问题

如果以上任何说法对您来说都是真的，我想与您分享一下，特别是如果您使用 Rails，并且只使用 ActiveRecord，这是与数据库通信的另一种方式。

这样，当需要实施我们将处理大量数据的功能时，您将有更多的选择。

### O que é o 续作？

如果您从未听说过 Sequel，就代码大小而言，此 gem 是 Ruby 的最小数据库 libs，而且它在启动应用程序时加载速度最快，因为它只加载核心和基本的银行通信功能！

根据您的需要，您可以通过“**插件”、“T1”添加其他功能，其中每个插件对应于 gem 中包含的单个文件，该文件仅在需要插件时加载“**”。****

这使他比其他人更快

> 例如，在干净类中，仅通过连接到数据库，ActiveRecord 的负载时间为 0.4 毫秒，而 sequence 的负载时间为 0.08 毫秒，速度提高了 5 倍！

续集的创刊号从创刊到现在一直保持着稳定和很好的支持，Jeremy Evans 的创造者，他将问题保持在接近零的状态，并且通常每月发布一次。

### 续集 vs ActiveRecord

#### 语法

两者的语法非常相似，这是因为在实现中不增加太多的复杂性=)

我们习惯于咨询银行，等待所有搜索记录载入后再返回使用，即“t0”*【preload】*。

#### 预载 x Layz 获取

sequel 在数据库查询中做*【休闲提取】* 。

然后你想:嗯，酷……那是什么？

有了休闲提取，每次搜索时他都会给你返回结果，不必等到所有记录都加载到内存中才能得到返回结果。

这给你带来了时间和记忆的节约。

#### [数据集](http://sequel.jeremyevans.net/rdoc/classes/Sequel/Dataset.html)

因为序列中的几乎所有查询都是由数据集表示的，所以大多数 dataset 方法都返回其自身的修改副本。它的返回是哈希格式，很容易处理。

它允许您返回同一数据集内多个表的信息，在同一搜索中重复使用和返回数据，因为您可以根据需要复盖数据集。

```
module DB::Version
 def dataset
 time = 1.week.ago
 original\_data = super
 table\_name = original\_data.first\_source\_alias

original\_data.from(Sequel.lit(\<\<-SQL, time, time))
 (
 SELECT \* FROM #{table\_name}
 WHERE sys\_period @\> ?::timestamptz
 UNION ALL
 SELECT \* from #{table\_name}\_history
 WHERE sys\_period @\> ?::timestamptz
 ) #{table\_name}
 SQL
 end
end 
```

在上面的例子中，我们有一个我刚才提到的例子。

在上面，我们复盖了类的原始方法*【数据集】* ，以便同时返回当前数据和保存记录更改的表数据=)

#### [续集& SQL](http://sequel.jeremyevans.net/rdoc/files/doc/sql_rdoc.html)

xs 开发人员喜欢编写代码而无需使用自定义 SQL 查询，这也是该系列中的 plus 之一！

有了它，就容易做到[【别名】](http://sequel.jeremyevans.net/rdoc/files/doc/sql_rdoc.html#label-Aliasing)、[型铸造](http://sequel.jeremyevans.net/rdoc/files/doc/sql_rdoc.html#label-Casts)、串操作、数学操作、银行结构操纵、观点等等。

#### 时间是金钱，我们不会分散

在数据库位置，数据库和应用程序之间几乎没有延迟：

当您的应用程序使用 AWS 等时，数据库和应用程序位于不同的服务器上和同一区域，等待时间接近半毫秒；

当您的数据库位于不同区域时，即使在同一数据中心，延迟也会超过 2 毫秒，依此类推……。

这意味着处理大量或大量数据需要的时间要长得多。

> 在 Smart FIT，我们有一份报告，只在巴西拍摄需要 12 小时。实施续集后，七国的上传时间降到了 8 点零几分！

这是因为通过实现顺序 EAGER 方法(即 eaguer load)，除了消除 N+1 外，默认情况下，在保存记录之前，我们还消除了 air 调用的回调。

此外，序列实现了 octopus，默认情况下，它进行多银行连接池管理，而随着 ActiveRecord，我们不得不添加到使用*【方法缺失】*使响应速度慢了 2 倍！

这些是 Sequel 提供的一些优势，因此自 2015 年以来 ActiveRecord 开始实施其中的一些优势。他的部署很简单:

```
gem install 'sequel-rails' ou adicioná-lo ao Gemfile

_Configuração inicial no_ **_application.rb_** _:_

config.sequel.after\_connect = proc do 
 Sequel::Model.plugin :timestamps, update\_on\_create: true 
 Sequel::Model.plugin :update\_or\_create 
 Sequel::Model.plugin :validation\_helpers 
 Sequel::Model.plugin :tactical\_eager\_loading 
end

Criação do seu model:

class DB::State \< Sequel::Model 
 one\_to\_many :cities 
end

class DB::City \< Sequel::Model 
 one\_to\_one :state 
end

e no IRB:
\> DB::State.where(uf: %w(sp rj))

Sequel::Mysql2::Database SELECT \* FROM `states` WHERE (`uf` IN ('sp'))
#\<DB::State [@values](http://twitter.com/values)={:id=\>1, :name=\>"São Paulo", :created\_at=\>2009-06-22 21:13:44 -0300, :updated\_at=\>2016-04-07 18:31:17 -0300, :uf=\>"SP"}\>

\> DB::State.where(uf: %w(sp)).eager(:cities).each do |state|
 p state
 p state.cities
end
 Sequel::Mysql2::Database SELECT \* FROM `states` WHERE (`uf` IN ('sp')) #\<DB::State [@values](http://twitter.com/values)={:id=\>1, :name=\>"São Paulo", :created\_at=\>2009-06-22 21:13:44 -0300, :updated\_at=\>2016-04-07 18:31:17 -0300, :uf=\>"SP"}
Sequel::Mysql2::Database SELECT \* FROM `cities` WHERE (`cities`.`state_id` = 1) [#\<DB::City [@values](http://twitter.com/values)={:id=\>79, :state\_id=\>11, :name=\>"Rio Claro", :created\_at=\>2009-06-22 21:13:54 -0300, :updated\_at=\>2016-06-09 08:06:44 -0300}...

Bem similar ao que conhecemos, não? 
```

你的文献中还有很多，希望我激起了你的好奇心，自己做一些测试和恶作剧！

那都是乡亲们！

​

​