# Rails 控制台魔术

> 原文：<https://dev.to/lfv89/rails-console-magic-tricks-1032>

# Rails 控制台魔术

提高 Rails 控制台会话期间开发人员体验的一些技巧。请注意，其中一些将来自 Ruby 本身，因此不一定局限于控制台。

## 会话结束后回滚

可以在一种称为沙箱的模式下运行控制台。在这种模式下，当会话终止时，对数据库所做的每个更改都将自动回滚:

```
$ rails console --sandbox 
```

## 检索以前的执行值

可以检索上一次控制台执行的结果，并如示例所示，通过调用 _:

```
>> Game.all.map(&:name)
=> ["zelda", "mario", "gta"]
>> names = _
=> ["zelda", "mario", "gta"] 
```

## 使用 grep 搜索方法

有可能找出只有一部分的方法的完整名称。通过从 Arrayone 调用 grep，可以对给定对象的方法执行方便的搜索:

```
>> Game.first.methods.grep(/lumn/)
Game Load (0.8ms)  SELECT  "games".* FROM "games" ORDER BY "games"."id" ASC LIMIT $1  [["LIMIT", 1]]
=> [:column_for_attribute, :update_column, :update_columns] 
```

## 找出方法位置

Object 类中的 source_location 方法返回方法文件定义的完整路径，包括定义它的行。在探索第三方库时，它可能特别有用:

```
>> 'Luis Vasconcellos'.method(:inquiry).source_location
=> ["/usr/local/bundle/gems/activesupport-5.2.1/lib/active_support/core_ext/string/inquiry.rb", 12] 
```

## 返回方法的源代码

虽然知道一个方法的确切位置很有意思，但是在某些情况下，将它的源代码直接输出到控制台可能会更好。这可以通过使用源方法来实现:

```
>> 'Luis Vasconcellos'.method(:inquiry).source.display
def inquiry
    ActiveSupport::StringInquirer.new(self)
  end
=> nil 
```

## 辅助对象

控制台提供了一个名为 helper 的对象，可用于从 Rails 应用程序中直接访问任何视图助手:

```
>> helper.truncate('Luis Vasconcellos', length: 9)
=> "Luis V..." 
```

## 应用程序对象

控制台还提供了一个有趣的对象，称为 app，它基本上是应用程序的一个实例。利用该目标可以实现以下目标:

*   访问`GET`端点

```
>> app.get('/')
Started GET "/" for 127.0.0.1 at 2018-08-25 22:46:52 +0000
   (0.5ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" ORDER BY "schema_migrations"."version" ASC
Processing by HomeController#show as HTML
  Rendering home/show.html.erb within layouts/application
  Rendered home/show.html.erb within layouts/application (11417.2ms)
  Rendered shared/_menu.html.erb (3.6ms)
  Rendered shared/header/_autocomplete.html.erb (292.2ms)
  Rendered shared/_header.html.erb (312.9ms)
  Rendered shared/_footer.html.erb (3.7ms)
Completed 200 OK in 11957ms (Views: 11945.5ms | ActiveRecord: 0.0ms)
=> 200 
```

*   访问`POST`端点

```
>> app.post('/games/zelda/wishlist_placements.js')
Started POST "/games/zelda/wishlist_placements.js" for 127.0.0.1 at 2018-08-25 23:03:21 +0000
Processing by OwnlistPlacementsController#create as JS
  Parameters: {"game_slug"=>"zelda"}
  Game Load (0.6ms)  SELECT  "games".* FROM "games" WHERE "games"."slug" = $1 LIMIT $2  [["slug", "zelda"], ["LIMIT", 1]]
  Rendering wishlist_placements/create.js.erb
  Rendered wishlist_placements/create.js.erb (194.8ms)
Completed 200 OK in 261ms (Views: 252.9ms | ActiveRecord: 0.6ms)
=> 200 
```

*   从`Game`路线中搜索`_path`助手:

```
>> app.methods.grep(/_path/).grep(/game/)
=> [:search_games_path, :game_ownlist_placements_path, :game_ownlist_placement_path, :game_wishlist_placements_path, :game_wishlist_placement_path, :game_path] 
```

*   以更有效的方式结合前面的技巧:

```
>> app.get(app.root_path)
Started GET "/" for 127.0.0.1 at 2018-08-26 02:27:40 +0000
Processing by HomeController#show as HTML
  Rendering home/show.html.erb within layouts/application
  Rendered home/show.html.erb within layouts/application (12550.2ms)
  Rendered shared/_menu.html.erb (3.8ms)
  Rendered shared/header/_autocomplete.html.erb (1.2ms)
  Rendered shared/_header.html.erb (28.0ms)
  Rendered shared/_footer.html.erb (3.8ms)
Completed 200 OK in 12835ms (Views: 12810.0ms | ActiveRecord: 0.0ms)
=> 200 
```

```
>> app.body.response
=> "\n<!DOCTYPE html>\n<html>\n  <head>\n     ... 
```

```
>> app.cookies
=> #<Rack::Test::CookieJar:0x0000556ee95c33e0 @default_host="www.example.com", @cookies=[#<Rack::Test::Cookie:0x0000556eeb72b2d0 @default_host="www.example.com", ... 
```