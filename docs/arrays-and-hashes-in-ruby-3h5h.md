# Ruby 中的数组和散列

> 原文：<https://dev.to/damcosset/arrays-and-hashes-in-ruby-3h5h>

## 简介

数组和散列是允许你一次存储多个值的数据结构。在本文中，我们将探索它们的语法，如何填充它们，检索值并遍历它们。我们走吧！

## 数组

下面是如何在 Ruby 中声明一个数组:

```
arr = [1, 4, "Hello", false] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，数组可以包含任何类型的数据。这些值包含在方括号 *[]* 中，用逗号分隔。

数组中的每一项都有一个编号索引。第一个项目的索引为 0，第二个项目的索引为 1，第三个项目的索引为 2...您可以通过使用括号符号中的索引来访问数组中的单个项目:

```
puts arr[2] #Third item => "Hello"
puts arr[0] #First item => 1 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记我们从零开始计数！这是:D 无数错误的根源

### 多维数组

因为数组可以包含任何类型的数据，所以它们也可以包含数组。一批...数组。我们称之为多维数组。内部数组的工作方式与任何其他数组完全相同，可以包含任何类型的数据。

```
multi_arr = [ [1, 4, "Hello"], [-54, false, "Ruby"] ] 
```

Enter fullscreen mode Exit fullscreen mode

访问这个数据结构中的值的方式与访问一个数组的方式相同，但是将括号符号链接起来。

```
puts multi_arr[0][2] # [First item][Third item in first item] => "Hello"
puts multi_arr[1][0] # [Second item][First item in second item] => -54 
```

Enter fullscreen mode Exit fullscreen mode

## 哈希

哈希是在一个变量中存储多个值的另一种方式。在数组中，您无法控制索引。它们是数字，每增加一项就增加一，从 0 开始。在 hash 中，提供键-值对，其中键不必是数字。

要创建一个新的散列，有两种可能:

```
hash = Hash.new # will create an empty hash

# OR

populated_hash = {
    "one" => "two",
    42 => false,
    true => "This is true",
    [1, 2, "Hello"] => "Key is an array??"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用的是。新方法，你**必须**大写 Hash。

如您所见，散列中的键可以是任何类型的数据。要向散列中添加一个新的键值对，可以使用如下的括号符号:

```
friend = "Joey"
hash["hello"] = "World"
hash[true] = "True"
hash[42] = "Answer Universe"
hash["Joey"] = friend

puts hash

# {"hello"=>"World", true=>"True", 42=>"Answer Universe", "Joey"=>"Joey"} 
```

Enter fullscreen mode Exit fullscreen mode

要访问 hash 中的值，使用括号中的键，就像这样:

```
puts hash["hello"] # World
puts hash[42] # Answer Universe
puts hash[true] # True 
```

Enter fullscreen mode Exit fullscreen mode

## 如何遍历数组和散列

您可以通过使用 *each* 方法遍历数组和散列。这两者之间的唯一区别是，当您循环访问一个散列时，您可以同时访问键和值。

```
arr = [1, 4, "Hello", false]

hash = {
  "hello" => "World",
  true => "True",
  42 => "Answer Universe",
  "Joey" => "Joey"
}

arr.each { |value|
    puts value
}

hash.each{ |key, value|
  puts "Key = #{key} / Value = #{value}"
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，数据结构中的当前值放在管道|value|之间。对于散列，用逗号分隔键和值。上面的代码将打印出以下内容:

```
 1
 4
 Hello
 false
 Key = hello / Value = World
 Key = true / Value = True
 Key = 42 / Value = Answer Universe
 Key = Joey / Value = Joey 
```

Enter fullscreen mode Exit fullscreen mode

## 嵌套循环

之前，我们看到了一种叫做多维数组的东西，数组嵌套在一个数组里面。你会怎么循环这个东西呢？你可以在父数组的循环中遍历子数组。像这样:

```
multi_arr = [ [1, 4, "Hello"], [-54, false, "Ruby"] ]

multi_arr.each{ |child_arr|
    child_arr.each{ |child_child_value| 
     puts child_child_value
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像这样，我们嵌套了所有的值:

```
 1
 4
 Hello
 -54
 false
 Ruby 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

现在，你知道 Ruby 中的散列和数组是什么了。您还知道如何从它们中检索值并遍历它们。下次见！玩得开心！