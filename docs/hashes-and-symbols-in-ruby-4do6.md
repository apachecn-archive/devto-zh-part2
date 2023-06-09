# Ruby 中的散列和符号

> 原文：<https://dev.to/damcosset/hashes-and-symbols-in-ruby-4do6>

## 简介

在这篇文章中，我们将继续探索散列，我们将发现什么是符号。为了复习一下哈希，我写了这个。

### 简介*零*

在 Ruby 中， *nil* 是一种表示值不存在的方式。所以，如果我们有一个散列，我们试图访问一个不存在的值:

```
my_hash = {
    "name" => "Damien",
    "age" => 26,
    "nationality": "French"
}

puts my_hash["lastName"] # puts nothing, because nil. lastName key doesn't exist 
```

没有抛出错误，Ruby 只是告诉你那里什么都不存在...没什么:D

但是，您可以在哈希中指定默认值。当 Ruby 在 Hash:
中找不到值时，将返回默认值而不是 nil

```
with_default = Hash.new("No value found here!!!")

puts with_default["name"]  # puts "No value found here!!!"
puts with_default[42] # puts "No value found here!!!" 
```

## 符号

让我们从语法开始。符号总是以冒号 *:* 开头，并且必须是有效的变量名:

```
my_symbol = :this_is_a_symbol 
```

符号可以用作哈希键或引用方法名。

### 哈希的密钥

符号是散列键的一个很好的用途，因为它们是不可变的。符号一旦定义，就不能更改。一次只能存在一个符号副本，因此它们不会消耗大量内存。这两个原因使得符号作为键比字符串作为键更快。

```
my_hash = {
    :one => "One",
    :a_symbol => 42,
    :boom => true
}

puts my_hash
# {:one=>"One", :a_symbol=>42, :boom=>true} 
```

#### 符号和字符串之间的转换。

Ruby 提供了将符号转换成字符串的方法，反之亦然。方法*。to_s* 将一个符号转换成一个字符串，而*。to_sym* 则相反。方法*。实习生*做的事情和*一样。to _ sym*T8】

```
puts :this_is_my_shiny_symbol.to_s # this_is_my_shiny_symbol
puts "look_at_this_string".to_sym # :look_at_this_string
puts "HelloWorld".intern # :HelloWorld 
```

#### 哈希火箭语法

您看到了我们在 hash 中的键和值之间使用了一个 *= >* 。这被称为哈希火箭语法。但是，您也可以使用较短的语法。为此，删除 *= >* ，并将冒号放在符号的末尾。语法类似于现在的 javascript 对象:

```
symbols_as_keys = {
    one: "two",
    three: "four"
} 
```

按键仍然是符号。但是，这种语法不适用于字符串作为键的情况。

### 更有选择性

当我们有了相关的键时，我们可以从散列中获取一个值。但是我们如何过滤一个散列来只检索满足某些标准的值呢？我们可以使用*。选择*

```
restaurant_ratings =  {
    soleia: 6,
    auberge_du_printemps: 3,
    pauls: 4,
    olive_garden: 7
} 
```

让我们使用 select 来过滤评级严格高于 5 的餐馆。

```
puts restaurant_ratings.select{ |restaurant, rating|
 rating > 5
}

# {:soleia=>6, :olive_garden=>7} 
```

现在，让我们得到评分等于 3 的餐馆

```
 puts restaurant_ratings.select{ |restaurant, rating| 
  rating == 3
}

# {:auberge_du_printemps=>3}` 
```

### 只是钥匙？或者只是价值观

因为 Ruby 很棒，如果你不需要键值*和*中的任何一个，你就不需要迭代它们。同*。each_key* ，你将只迭代键和*。each_value* ，你将只得到值:

```
restaurant_ratings.each_key{|k| puts k}

# soleia
# auberge_du_printemps
# pauls
# olive_garden

restaurant_ratings.each_value{|v| puts v}

# 6
# 3
# 4
# 7 
```