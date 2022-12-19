# Ruby 中的循环

> 原文：<https://dev.to/damcosset/loops-in-ruby-3hl4>

## 简介

我继续我的 Ruby 之旅:)这一次，循环通过的东西！

### *而*

在 Ruby 中有几种方法可以设置循环。在很多语言中，最常见的一个是 *while* 循环。像这样:

```
i = 0

while i < 10
  puts i
  i += 1
end 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*可以用 *+=1* 作为`i = i + 1`的快捷键。同样，您可以使用-=、*=和/=进行其他操作。

### *直到*

从*到*的循环类似于时的*，但却是反向的。基本上就是说，当条件为假时，执行 until 循环中的代码:* 

```
i = 1

until i == 16 # as long as this condition is false, run the loop
  i *= 2
end

puts i #16 
```

Enter fullscreen mode Exit fullscreen mode

### 为/在

for in 循环遍历一系列数字。您可以指定是否必须包括或排除范围内的最后一个数字。

```
for n in 1..10  # Includes 10
  puts n # stops at 10
end

for n in 1...10 # Excludes 10
  puts n # stops at 9
end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，如果您在该范围的第一个和最后一个数字之间键入 2 个点，这意味着您包括了最后一个数字。三个点表示最后一个数字不包括在内。

### 循环

*循环*是一个 Ruby 方法，反复调用同一个代码块。

```
loop do 
  puts "Hello" 
end 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个无限循环，无限次打印“Hello”。

*注:*可以用花括号 *{}* 代替 *do/end* 。所以，这是一回事:

```
loop {
  puts "Hello"
} 
```

Enter fullscreen mode Exit fullscreen mode

显然，无限循环并不总是想要的。为了打破这个循环，你可以使用*打破*关键字:

```
i = 5
loop {
  puts i # prints 5 through 20
  i += 1
  break if i > 20 
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用 *next* 关键字跳到下一个迭代

```
for n in 0...10 
 next if n == 3
 puts n
 break if n == 5
end 
```

Enter fullscreen mode Exit fullscreen mode

这将打印 0，1，2，4，5。为什么？因为当计数器 *n* 等于 3 时，我们调用 *next* ，这意味着代码块的剩余部分没有被执行，因此我们不执行 *puts* 命令。当计数器 *n* 达到 5 时，我们遇到 *break* 语句并离开循环(在调用 *puts* 之后)

### [T1。倍](#times)

*。times* 方法是一个紧凑形式的 for 循环。语法读起来甚至像英语:

```
3.times do puts "Hello Ruby!" end 
```

Enter fullscreen mode Exit fullscreen mode

你能猜到打印出来的是什么吗？

```
Hello Ruby!
Hello Ruby!
Hello Ruby! 
```

Enter fullscreen mode Exit fullscreen mode

您指定希望循环运行的次数，后跟*。次*法。然后，指示每次要执行的代码块。当然，你可以把 *do/end* 关键词换成花括号:

```
3.times { puts "Hello Ruby!" } 
```

Enter fullscreen mode Exit fullscreen mode

### 循环遍历数组

数组是在一个变量中存储多个值的一种方式。要在 Ruby 中声明一个数组，可以使用 *[]* :

```
iAmAnEmptyArray = []

# OR

arrayWithStuff = [1, 42, "Hello"] 
```

Enter fullscreen mode Exit fullscreen mode

Ruby 给你*。每个*方法，以便在数组中循环。注意语法:

```
arr = [1, 2, "Hello", false]
arr.each { |a|
 puts a 
} 
```

Enter fullscreen mode Exit fullscreen mode

```
1
2
Hello
false 
```

Enter fullscreen mode Exit fullscreen mode

您在每个通道中检索的值在管道 *||* 之间声明。当然，你可以用任何你认为合适的方式处理这些值:

```
arr = [1, 2, 3, 4, 5]

arr.each { |x|
 puts x + 1
}

arr2 = ["World", "Ruby", "Mom"]

arr2.each { |w|
  puts "Hello #{w}!"
} 
```

Enter fullscreen mode Exit fullscreen mode

```
2
3
4
5
6
Hello World!
Hello Ruby!
Hello Mom! 
```

Enter fullscreen mode Exit fullscreen mode

注意在第二个循环中使用字符串内变量名的语法: *#{variable_name}* 。

好了，我想这对于这篇文章来说已经足够了。我们下次再见，看更多 Ruby noob 的东西！

玩得开心！