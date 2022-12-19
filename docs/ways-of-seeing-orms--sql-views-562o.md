# 查看方式:ORMS 和 SQL 视图

> 原文：<https://dev.to/annarankin/ways-of-seeing-orms--sql-views-562o>

本文假设您对数据库、Ruby 和 Rails 的工作原理有一个大致的了解——下面的例子使用了 [ActiveRecord](https://guides.rubyonrails.org/active_record_basics.html) 库将数据库关系映射到 Rails 应用程序上下文中的 Ruby 对象。查看[范例库](https://github.com/annarankin/vet-orm-ruby-example)，看看它们的运行情况！(注意:如果你感兴趣，我还在这里设置了一个 JavaScript 示例，但是我还没有找到一个对 SQL 视图有一流支持的 JS ORM。)

## 聚集加重

Web 应用程序通常显示“汇总”或聚合数据，这些数据可以从多个数据库表中提取信息并进行计算。ORM(对象关系映射)数据库建模库通常被设计成一次从一个表中读取数据。虽然这种模式对于大量用例非常有用(基本的 CRUD 对于成功非常有用)，但是当我们试图聚合数据时，我们会面临复杂的应用程序代码和/或昂贵的数据库查询的风险。SQL 视图有可能帮助我们减少查询次数，同时将数据聚合逻辑下推到数据库中。

举个例子——假设你被雇佣来开发一个应用程序，帮助兽医和他们的客户交流。如果您已经创建了一个服务器端点:
,您可能会对这样的工作流(精简后的)示例很熟悉

```
user = User.includes(:pets).find(1)

render json: {
  user: {
    id: user.id,
    name: "#{user.first_name}  #{user.last_name}"
  },
  pets: user.pets.map do |pet|
    {
      id: pet.id,
      name: pet.name
    }
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

[![seated dog on blue background](../Images/62b069a530a66eed4ef9af368a99c815.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NkRG69g2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h61mba0idrsuqrbcd95k.png)

上面的代码非常简单:它加载一个用户，加载他们的宠物，并以前端期望的格式序列化数据。我们正在进行两个查询，但是它们很简单并且(希望)很快——这是一个合理的期望。

几周之后，我们有了新的需求。用户可以看到他们向你注册了哪些宠物，但他们也想“一目了然”地知道他们的下一次约会是什么时候！因此，您更新了端点:

```
user = User
  .includes(:pets)
  .find(1)

# Pre-load the user's pets' appointments that are scheduled for the future
upcoming_appointments = user
  .appointments
  .order(date: :asc)  
  .where('date >= ?', Time.current)

render json: {
  user: {
    id: user.id,
    name: "#{user.first_name}  #{user.last_name}"
  },
  pets: user.pets.map do |pet|
    # Use Array#find to return the first of this pet's appointments
    next_appointment_date = upcoming_appointments.find do |appt|
      appt.pet_id == pet.id
    end

    {
      id: pet.id,
      name: pet.name,
      next_appointment_date: next_appointment_date
    }
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

老实说，这还不算太糟糕。我们添加了另一个查询——这次是关于约会——带有一些排序和过滤逻辑。我们还在序列化步骤中添加了一些循环逻辑，为主人的每只宠物提取第一个约会。这有点不整洁，但是，嘿，*它的作品。*

就我个人而言，我不喜欢在应用程序代码中编码所有这些行为。对我来说这感觉很乱，而且邪恶的东西很容易溜进来。例如，假设我们这样做了:

```
render json: {
  user: {
    id: user.id,
    name: "#{user.first_name}  #{user.last_name}"
  },
  pets: user.pets.map do |pet|
    next_appointment_date = user
      .appointments
      .order(date: :asc)
      .find_by('date >= ?', Time.current)
      &.date

    {
      id: pet.id,
      name: pet.name,
      next_appointment_date: next_appointment_date
    }
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

🙀哦不！有人偷偷在我们的序列化步骤中添加了一个查询，虽然这个*可能*一开始没有敲响任何警钟*(当你每个用户处理一到两只宠物时)，但是当当地的动物收容所成为你的客户之一并注册了一百多只宠物时会发生什么呢？每页加载超过一百个查询。*

这显然是一个虚构的例子，但是我遇到过更复杂的情况，其中 N+1 个查询隐藏在服务对象和单独的文件中。我个人喜欢将这种逻辑推下一层——到数据库中——当它开始变得更复杂时，或者当我在应用程序的其他地方需要它时。这就是 SQL 视图的用武之地！

## 什么*是 SQL 视图？*

 *我心目中最基本的 SQL 视图模型是“保存在数据库中的查询，它的作用就像一个表。”除此之外，还有很多，但是这种简单的理解可以让你走得更远。例如，如果我在数据库中执行以下语句:

```
CREATE VIEW silly_users AS
  SELECT 
    id, 
    first_name,
    first_name || ' Mc' || first_name || 'erson' AS silly_name
  FROM users; 
```

Enter fullscreen mode Exit fullscreen mode

*[更上一层楼`||`串联运算符](http://www.postgresqltutorial.com/postgresql-concat-function/)*

我可以使用与查询表相同的语法查询这个视图的结果:

```
# SELECT * FROM silly_users;
  id   | first_name |       silly_name       
------------+------------+------------------------
     1 | Melissa    | Melissa McMelissaerson
     2 | Colleen    | Colleen McColleenerson
     3 | Vince      | Vince McVinceerson
     4 | David      | David McDaviderson
     5 | Dennis     | Dennis McDenniserson
...etc 
```

Enter fullscreen mode Exit fullscreen mode

因为这个视图的行为非常像一个表，所以它可以很好地与 ORM 一起使用。我们可以创建一个视图支持的模型！

```
silly_user = SillyUser.find(1)
silly_user.silly_name // => 'Melissa McMelissaerson' 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用 Ruby，我强烈推荐 ThoughtBot 的 [Scenic gem，它为使用 ActiveRecord ORM 的项目带来了视图版本控制和其他有用的特性。](https://github.com/thoughtbot/scenic)

## 新的视角

让我们尝试编写一个数据库视图来获取我们想要的数据，而不是在我们的应用程序代码[中查询它(更多关于使用`DISTINCT ON`和`GROUP BY`](https://robots.thoughtbot.com/ordering-within-a-sql-group-by-clause) ):

```
-- Grabs one record per pet, returning the earliest future appointment date 
CREATE VIEW pets_with_upcoming_appointments AS
SELECT DISTINCT ON (pets.id)
  pets.id AS id,
  users.id AS user_id,
  pets.name AS name,
  MIN(appointments.date) AS next_appointment_date
FROM users
INNER JOIN pets
  ON user_id = users.id
LEFT JOIN appointments
  ON pets.id = pet_id
  AND appointments.date >= CURRENT_DATE
GROUP BY (
  users.id,
  pets.id,
  pets.name
); 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们可以从这个观点来看:

```
# SELECT * FROM pets_with_upcoming_appointments;

 user_id | pet_id | pet_name |  next_appointment_date   
--------------+--------+----------+-----------------------
       1 |      1 | Flannery |   2018-11-22 13:00:00
       2 |      2 | Porkchop |   2018-11-22 16:30:00
       2 |      3 | Gravy    |   2018-12-01 09:00:00
       3 |      4 | Magnus   | 
       4 |      5 | Huey     |   2018-12-15 10:45:00
       4 |      6 | Duey     |   2018-12-15 10:45:00
       4 |      7 | Louie    |   2018-12-15 10:45:00

# SELECT * FROM pets_with_upcoming_appointments WHERE user_id = 1;

 user_id | pet_id | pet_name |  next_appointment_date   
--------------+--------+----------+-----------------------
       1 |      1 | Flannery |   2018-11-22 13:00:00 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经建立了视图，我们可以使用前面提到的 Scenic gem 创建一个迁移，然后将它连接到一个数据库支持的 ORM 模型:

```
class PetWithUpcomingAppointment < ActiveRecord::Base
  self.table_name = 'pets_with_upcoming_appointments'
end 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的视图有一个`user_id`字段，所以在我们的用户模型中建立一个关系很简单:

```
class User < ActiveRecord::Base
  has_many :pets
  # wooooot
  has_many :pet_with_upcoming_appointments
  has_many :appointments, through: :pets
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以清理我们的数据获取应用程序代码:

```
user = User
  .includes(:pet_with_upcoming_appointments)
  .find(params[:id])

render json: {
  user: {
    id: user.id,
    name: "#{user.first_name}  #{user.last_name}"
  },
  pets: user.pet_with_upcoming_appointments.map do |pet|
    {
      id: pet.id,
      name: pet.name,
      next_appointment_date: pet.next_appointment_date
    }
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

还不错！我们回到两个查询，控制器中没有排序/日期比较逻辑💪更好的是，我们可以在应用程序的其他部分重用这个模型，而无需复制查询逻辑。当您开始执行更复杂的聚合并从不同的表中提取数据时，这一点真正开始显现出来。

## SQL 视图的陷阱

[![cat peeking up out of a hole](../Images/6d5663dd99d7c8f952fbaee2676870c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UtaUk66j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xnojoit50o3hbssbxh4.png)

虽然我显然是一个粉丝，但是当您开始考虑应用程序中的视图时，有一些事情您应该注意:

### 过分热心的观察

这是“我有一把锤子，一切都是钉子”的另一个方面问题。当您第一次开始使用它们时，您会发现自己将*太多的逻辑压入数据库中(在那里它们被抽象出来，更难找到)。您最终不得不运行大量的迁移，因为每次您需要更改应用程序提供的内容时，您都需要更新或创建一个新的视图。😬在你把你正在做的任何事情变成一个视图之前，问问你自己你正在获得的利益是否值得这个交换。*

### 层叠视图层次过多

视图通常由表中的结果组成，但是您也可以创建一个视图，从另一个视图(元视图！？).这似乎是一个好主意(“我将在这个视图中定义什么是‘活跃’用户，然后在我需要用户的任何地方把它拉进来！”)，但是在实践中，我看到它使得更新所有级别的视图变得更加困难。你也可以把自己逼入绝境，试图弄清楚一个视图的变化如何影响另一个视图，而另一个视图从几个层次上获取数据😵这是一个与第一点相关的问题。

### 传播效率低下

如果您熟悉对 SQL 查询进行概要分析，并使用`EXPLAIN` / `EXPLAIN ANALYZE`来发现问题(或者如果上面视图中的`LEFT JOIN appointments`让您不安😂)，这是运用这些技能的好时机！如果[索引](https://www.tutorialspoint.com/postgresql/postgresql_indexes.htm)、[散列连接](https://malisper.me/postgres-hash-joins/)和[顺序扫描](https://malisper.me/postgres-sequential-scans/)让您头晕目眩，那么一旦您开始在数据库中获取更多记录，看似简单的查询可能会运行得非常慢。如果你不小心的话，低效的查询最终会支持你的应用程序的大量功能，导致整体性能缓慢。至少，检查一下引入一个视图对使用它的过程的速度的影响。

## 现在怎么办？

如果你感兴趣，这里有一些我推荐的资源(以及我在这篇文章中引用的链接)来了解更多！长寿，繁荣，不要害怕数据库🖖

*   [回购示例](https://github.com/annarankin/vet-orm-ruby-example)
*   [什么是关系数据库视图？](https://www.essentialsql.com/what-is-a-relational-database-view/)
*   [如何使用视图](https://www.periscopedata.com/blog/how-to-use-views)
*   [SQL Group By 子句内的排序](https://robots.thoughtbot.com/ordering-within-a-sql-group-by-clause)
*   [德佩什解释`EXPLAIN`输出的工具](https://explain.depesz.com/)
*   [索引简介](https://www.tutorialspoint.com/postgresql/postgresql_indexes.htm)
*   [散列连接](https://malisper.me/postgres-hash-joins/)
*   [连续扫描](https://malisper.me/postgres-sequential-scans/)
*   [索引扫描](https://malisper.me/postgres-index-scans/)*