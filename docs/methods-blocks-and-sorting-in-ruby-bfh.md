# Ruby 中的方法、块和排序

> 原文：<https://dev.to/damcosset/methods-blocks-and-sorting-in-ruby-bfh>

## 简介

随着我不断学习这门语言的基础知识，我的 Ruby 之旅还在继续。这一次，我探索方法、块和排序。让我们看看我是否理解对了。

## 方法

方法是为了执行特定任务而编写的可重用代码。编写方法使你的代码可读性更强，冗余性更小，更容易测试。

### 方法语法

一个方法有一个头和一个体。头由关键字 *def* 和方法名组成。主体是调用方法时执行的一段代码。最后，当你完成了主体，你使用关键字*结束*，以表明下面的代码与方法无关。

```
def say_hi
    puts "Hi!"
end 
```

Enter fullscreen mode Exit fullscreen mode

定义是好的，但是我们需要调用方法来执行方法体。
要调用一个方法，使用它的名字:

```
say_hi # Hi! 
```

Enter fullscreen mode Exit fullscreen mode

## 自变量和参数

你可以给方法提供参数，比如:

```
say_hi("John") 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们的方法必须预料到这些参数。您必须修改方法定义以包含*参数*。参数是参数的占位符，而方法等待被调用:

```
def say_hi(name) # <= name is the parameter
    puts "Hi #{name}!"
end

say_hi("John") # <= John is the argument
# Hi John! 
```

Enter fullscreen mode Exit fullscreen mode

### 劈头盖脸的争论

如果我们给我们的 say_hi 方法一个以上的参数会发生什么？

```
say_hi("John", "Paul", "Sarah") 
```

Enter fullscreen mode Exit fullscreen mode

我们得到以下错误:
`wrong number of arguments (given 3, expected 1) (ArgumentError)`

如果你不知道一个方法将接收多少个参数，你可以使用叫做 *splat arguments* 的东西。要告诉一个方法多于它所期望的参数数量，可以在参数名前加一个*号:

```
def say_hi(*names)
    names.each{|name|
    puts "Hi #{name}!"
  }
end

say_hi("John", "Joe", "Sarah")

# Hi John!
# Hi Joe!
# Hi Sarah! 
```

Enter fullscreen mode Exit fullscreen mode

### 从一个方法返回

打印很好，但是大多数情况下，您需要从方法中执行的操作返回一些东西。为此，您将使用 *return* 关键字。

```
def multiply(a, b)
    return a*b
end

multiply(2, 3) # returns 6
multiply(4, 5) # returns 20 
```

Enter fullscreen mode Exit fullscreen mode

方法可以返回你想要的任何东西，而不仅仅是数字。布尔例如:

```
def isEven?(a)
    return a % 2 == 0
end

isEven?(3) # returns false
isEven?(10) # returns true 
```

Enter fullscreen mode Exit fullscreen mode

或者其他方法:

```
def returnsMethod(a, b)
    return multiply(a, b)
end

returnsMethod(5, 6) # returns 30 
```

Enter fullscreen mode Exit fullscreen mode

注意:最后一个例子返回了返回一个数字的*乘*方法。

## 排序

Ruby 提供了一个名为 *sort* 的内置排序方法。

```
arr = [5, 2, 3, 1, 4, 5, 3]
arr.sort! # [1, 2, 3, 3, 4, 5, 5]

#OR

new_array = arr.sort 
```

Enter fullscreen mode Exit fullscreen mode

请记住，添加！在方法结束时，对原始变量进行变异。您也可以省略它，并将结果存储在一个新变量中。排序也适用于字符串:

```
movies = ["1984", "Notre-Dame de Paris", "I am a legend", "Clean Code"]
movies.sort! # ["1984", "Clean Code", "I am a legend", "Notre-Dame de Paris"] 
```

Enter fullscreen mode Exit fullscreen mode

### 组合比较运算符

Ruby 还有一个比较两个值的运算符，叫做组合比较运算符: *< = >*

如果第一个值在第二个值之前，则返回 **-1** ，如果

# 正好相反。最后，如果两项相等，它将返回 0。

```
a = 1
b = 4

a <=> b # -1
b <=> a # 1 
```

Enter fullscreen mode Exit fullscreen mode

### 按降序排序

默认情况下，sort 方法按升序排序。您可以提供一个块作为按降序排序的可选参数。您可以使用组合比较运算符。

```
movies.sort! {|firstMovie, secondMovie| secondMovie <=> firstMovie}
# ["Notre-Dame de Paris", "I am a legend", "Clean Code", "1984"] 
```

Enter fullscreen mode Exit fullscreen mode

玩得开心！