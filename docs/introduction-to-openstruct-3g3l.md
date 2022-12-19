# OpenStruct 简介

> 原文：<https://dev.to/cseeman/introduction-to-openstruct-3g3l>

OpenStruct 简介

[![](../Images/d40530bc28c10a217c9e0790566a734b.png)](https://i.giphy.com/media/LHZyixOnHwDDy/giphy.gif) 
*(原因每篇文章都需要一点 gif)*

上周，我接到一个任务，开始处理从数据库中提取的一些 CSV 数据。已经存在处理数据的代码，所以我需要确保无论应用什么更改，它们都是向后兼容的。数据是数组的 csv 表示形式。当前的代码是硬编码的，以在一个相当严格的过程中遍历数组，所以为了增加一些灵活性，并允许更好地访问控制器中的数据，建议使用 OpenStruct。

首先快速概述一下什么是 OpenStruct，它是一种类似于哈希的数据结构。它应用带有伴随值的任意属性。这看起来很神奇，但这只是 Ruby 的元编程，它定义了类本身的方法。在对象上使用 OpenStruct 的一个基本例子可能是:

```
require "ostruct"

candy = OpenStruct.new
candy.name = "Snickers"
candy.type = "Chocolate"
candy.amount = 5 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果我需要调用并找出糖果的名称或类型，我可以调用对象本身的一个方法(而不是像 Ruby hash 方式那样调用 candy[:name] # => "Snickers")

```
candy.name
> "Snickers"
candy.type
> "Chocolate" 
```

Enter fullscreen mode Exit fullscreen mode

所以代码从使用:

```
@csv = CSV.parse(parsed_text) 
```

Enter fullscreen mode Exit fullscreen mode

以 OpenStruct:

```
@csv = CSV.parse(parsed_text, headers: true).map { |row| OpenStruct.new(row.to_h) } 
```

Enter fullscreen mode Exit fullscreen mode

利用 csv 数据现在从:

```
@list = @csv[1..-1] || []
@count = @list.count 
```

Enter fullscreen mode Exit fullscreen mode

而现在:

```
@list = @csv.map { |row| row.to_h.values } || []
@count = @csv.count 
```

Enter fullscreen mode Exit fullscreen mode

访问这些值要容易得多，但是使用 OpenStruct 时需要注意一点:

1.  它不是 performant，这段代码不需要记住这一点，但是如果你确实需要记住这一点，就用 hash 或 struct。
2.  OpenStruct！= Struct，Struct 类似于自定义创建的类，不需要初始化，也没有任何 attr。Struct 更高性能，OpenStruct 更灵活。Struct 必须定义属性，而 OpenStruct 是动态的。为了前任。我们有名称，类型和数量，但不能添加在以后的人口数量列表。

```
candy = Struct.new(:name, :type, :amount)
snickers = candy.new("Snickers", "Chocolate", 5)

snickers.type 
> "Snickers" 
```

Enter fullscreen mode Exit fullscreen mode