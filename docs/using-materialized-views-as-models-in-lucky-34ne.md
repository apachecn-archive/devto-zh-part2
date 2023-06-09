# 在 Lucky 中使用物化视图作为模型

> 原文：<https://dev.to/mikeeus/using-materialized-views-as-models-in-lucky-34ne>

我有一个应用程序，在 Postgresql 数据库中有超过 200 万行，我用连接、联合和聚合函数进行复杂的查询，这需要很多时间。其中一些查询的结果被大量使用，例如，在应用程序的主页上呈现一个 cloropleth 地图，该地图根据地图上的过滤器而变化。这是一个超级昂贵的操作，无法扩展！

物化视图来帮忙了。物化视图是一种查询，它像表一样被持久化，并以同样的方式被处理。这意味着这个表上的查询很快。没有联接或联合，就像查询任何其他表一样。请注意，它需要刷新以保持最新，因此不适合实时数据。

虽然我们可以使用`DB.mapping` 将物化视图上的查询[映射到一个普通的水晶类，但是我们会错过 Lucky 令人敬畏的类型安全查询。因此，在本文中，我们将像对待普通表一样对待物化视图，并让 Lucky 为我们建模！](https://dev.to/mikeeus/mapping-database-queries-to-crystal-classes-with-crystal-db-and-lucky-460i)

## 复杂岗

下面是我们将要具体化的查询的 sql。稍后我们会对它做一些小的调整，这样它就可以很好地与`LuckyRecord::Model`配合。

```
SELECT
  posts.id,
  posts.title,
  posts.content,
  users.name as author
FROM posts
JOIN users
ON users.id = posts.user_id 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个复杂的查询，但是我们实现这个特性的方式可以扩展到包括任何查询，不管它有多复杂。唯一重要的是由我们的`SELECT`语句返回的列匹配我们的`LuckyRecord`模型定义。

## 设置

如果你想亲自测试，你可以使用我的演示应用程序，只需克隆回购并检查`matviews-0`分支，或`matviews-3-complete`查看完成的代码。

```
git clone git@github.com:mikeeus/lucky_api_demo.git
cd lucky_api_demo
bin/setup
git checkout matviews-0 
```

Enter fullscreen mode Exit fullscreen mode

## 创建规格

**git checkout matviews-0**

尽管我们没有物化视图或模型来访问它，但我们将编写一个规范，说明我们希望*如何能够使用它。* 

```
# spec/matview_spec.cr
require "./spec_helper"

describe App do
  describe "ComplexPost matview" do
    it "should refresh and query" do
      user = UserBox.new.create
      first_post = PostBox.new.title("First").user_id(user.id).create
      second_post = PostBox.new.title("Second").user_id(user.id).create

      ComplexPost.refresh

      complex = ComplexPostQuery.new.title(first_post.title).first
      complex.title.should eq first_post.title
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够使用模型刷新物化视图，并且我们希望使用 Lucky 自己的查询来查询视图，这些查询将为每一列生成 helper 方法，例如:`ComplexPostQuery.new.title(...)`、`ComplexPostQuery.new.content.ilike(...)`等。

## 创建物化视图

**git check out mat views-1-create-mat view**

首先让我们用`lucky gen.migration CreateComplexPostMatview`生成一个迁移。

```
class CreateComplexPostsMatview::V20180101010 < LuckyMigrator::Migration::V1
  def migrate
    execute <<-SQL CREATE MATERIALIZED VIEW complex_posts AS
        SELECT
          posts.id,
          posts.title,
          posts.content,
          users.name as author,
          NOW() as created_at,
          NOW() as updated_at
        FROM posts
        JOIN users
        ON users.id = posts.user_id SQL

    execute "CREATE UNIQUE INDEX complex_posts_id_index ON complex_posts (id)"
  end

  def rollback
    execute "DROP MATERIALIZED VIEW complex_posts"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们需要有`id`、`created_at`和`updated_at`列，因为`LuckyRecord::Model`在为我们生成助手方法时会用到它们。在这种情况下，我们实际上使用了`id`,甚至在其上添加了一个惟一的索引来加速我们的查询，但是我应该注意到**对于一个物化视图**来说，既不需要 id 列也不需要主键。

现在，如果我们使用`lucky db.migrate`进行迁移，应该会顺利通过。

## 生成 ComplexPost 模型并查询

**git check out matviews-2-create-model**

现在我们在数据库中有了物化视图，我们可以定义一个调用`DB.mapping`的`ComplexPost`类(就像我以前的文章一样),然后像这样用`LuckyRecord::Repo`查询它。

```
LuckyRecord::Repo.run do |db|
  db.query_all "SELECT * FROM complex_posts", as: ComplexPost
end 
```

Enter fullscreen mode Exit fullscreen mode

但这不会给我们带来`LuckyRecord::Model`或`ComplexPost::Query`的好处。

因此，我们将通过运行`lucky gen.model ComplexPost`来生成我们的`ComplexPost`模型。我们可以继续删除生成的`ComplexPost::Form`,因为我们永远不会在我们的物化视图中插入行。

让我们把它当作普通的数据库模型来填充，并添加一个类方法来刷新物化视图。

```
# src/models/complex_post.cr
class ComplexPost < BaseModel
  table :complex_posts do
    column title : String
    column content : String
    column author : String
  end

  def self.refresh
    LuckyRecord::Repo.db.exec "REFRESH MATERIALIZED VIEW complex_posts"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 运行规范

**git 克隆 mat views-3-完成**

运行`crystal spec spec/matviews_spec.cr`并...嘭！起作用了。

虽然这是一个简单的例子，但我们可以对其进行扩展以解决复杂的查询。我在应用程序中使用的查询示例如下。

```
class CreateCountryAnnualTradeMatview::V0000001 < LuckyMigrator::Migration::V1
  def migrate
    execute <<-SQL CREATE MATERIALIZED VIEW country_annual_trades AS
      SELECT
        merged.id, -- FOR LUCKY
        merged.name,
        merged.short,
        merged.year,
        COALESCE(sum(merged.total_imports_cents))::bigint as total_imports_cents,
        COALESCE(sum(merged.total_exports_cents))::bigint as total_exports_cents,
        NOW() as created_at, -- FOR LUCKY
        NOW() as updated_at -- FOR LUCKY
      FROM (
        ( SELECT ... ) -- some complex query
        UNION
        ( SELECT ... ) -- another complex query
      ) merged
      GROUP BY id, name, short, year
      ORDER BY name SQL

    execute <<-SQL CREATE UNIQUE INDEX country_annual_trades_id_year -- we can even add indexes!
        ON country_annual_trades(id, year) SQL
  end

  def rollback
    execute <<-SQL DROP MATERIALIZED VIEW country_annual_trades SQL
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

还有模型。

```
class CountryAnnualTrade < BaseModel
  table :country_annual_trades do
    column name : String
    column short : String
    column year : Int32
    column total_imports_cents : Int64
    column total_exports_cents : Int64
  end

  def self.refresh
    LuckyRecord::Repo.db.exec "REFRESH MATERIALIZED VIEW country_annual_trades"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

注意，我将聚合列`total_imports_cents`和`total_exports_cents`转换为`::bigint`，然后在模型中将它们定义为带有`column total_imports_cents : Int64`的`Int64`。

## 加入我们

我希望你喜欢这个教程，并发现它很有用。加入我们的 [Lucky gitter 频道](https://gitter.im/luckyframework/Lobby)了解框架的最新进展，或者[查看文档](https://luckyframework.org/guides)了解如何通过 Lucky 将您的应用理念付诸实践的更多信息。