# 用 Ruby 制作一个小的命令行应用程序

> 原文：<https://dev.to/damcosset/making-a-little-command-line-application-with-ruby-536c>

## 简介

在第一篇探索 Ruby 与 irb 的关系的文章之后，我继续探索这种语言。这一次，我将制作一个小的命令行工具来学习如何操作字符串以及如何提示用户输入。

## 简单用户提示

让我们创建一个名为 *index.rb* 的文件，并询问用户它的名字:

```
print "First Name? "
f_name = gets.chomp 
```

Enter fullscreen mode Exit fullscreen mode

我们只是在终端上打印一个问题。通过使用*获取*，我们表明我们正在等待用户输入。所以程序会等待一个答案(它实际上是在等你按回车键)。然后，我们将答案存储到一个名为 *f_name* 的变量中。

*注:**。chomp* 方法将删除字符串末尾的换行符。所以，你可以只使用 *gets* ，但这将删除任何不必要的空白，以备后用；)

在你的终端中运行 *ruby index.rb* 。你会看到类似于

```
➜  ruby git:(master) ✗ ruby index.rb
First Name? 
```

Enter fullscreen mode Exit fullscreen mode

我们在等你按回车键。所以，你可以输入任何你想要的，按下回车键，程序就会关闭。

现在，不幸的是，我们的小东西和变量没有任何关系。让我们把它打印回终端:

```
print "First Name? "
f_name = gets.chomp
print "Your first name is #{f_name}." 
```

Enter fullscreen mode Exit fullscreen mode

注意#{}用变量名告诉 Ruby 它需要检索存储在名为 *f_name* 的变量中的值。

```
➜  ruby git:(master) ✗ ruby index.rb
First Name? Damien
Your first name is Damien. 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们夺回来了！

## Moar 提示！！！

当然，你可以在你的小应用中添加更多的问题。

```
print "First Name? "
f_name = gets.chomp
print "Age? "
age = gets.chomp
print "City? "
city = gets.chomp
print "Country? "
country = gets.chomp

print "My name is #{f_name} and I am #{age} years old. I live in #{city}, #{country}." 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们还要求用户的年龄，城市和国家。我们使用与之前相同的 *gets.chomp* 函数，但是将答案存储在不同的变量中。

```
➜  ruby git:(master) ✗ ruby index.rb
First Name? damien
Age? 26
City? paris
Country? france
My name is damien and I am 26 years old. I live in paris, france. 
```

Enter fullscreen mode Exit fullscreen mode

太好了！它像预期的那样工作。然而，正如您所看到的，我用小写字母输入了所有内容，因此所有内容都以小写字母显示。让我们操纵我们的答案，使它变得漂亮

## 琴弦操作

### 大写

*大写*方法改变第一个字母，使其大写。在我们的例子中，有两种做事方式:

*   创建一个新变量来存储我们的更改
*   立即更改变量的值

```
# First way
f_name = gets.chomp
f_name_two = f_name.capitalize

# Second way
f_name = gets.chomp.capitalize! 
```

Enter fullscreen mode Exit fullscreen mode

两者都会做同样的事情。如果使用第一种方法，只需确保使用了正确的变量名。请注意“！”在方法名后用第二种方式来指示 Ruby 改变当前变量值并仍然把它存储在 *f_name* 中。所以，我们的程序现在看起来是这样的:

```
print "First Name? "
f_name = gets.chomp.capitalize!
print "Age? "
age = gets.chomp
print "City? "
city = gets.chomp.capitalize!
print "Country? "
country = gets.chomp.capitalize!

print "My name is #{f_name} and I am #{age} years old. I live in #{city}, #{country}." 
```

Enter fullscreen mode Exit fullscreen mode

而结果是:

```
➜  ruby git:(master) ✗ ruby index.rb
First Name? damien
Age? 26
City? paris
Country? france
My name is Damien and I am 26 years old. I live in Paris, France. 
```

Enter fullscreen mode Exit fullscreen mode

### 上升

我不希望国名这样显示。我要全部用大写字母。Ruby 提供了 *upcase* 方法。它的用法和以前的大写方法一样。替换*大写！*从`country = gets.chomp.capitalize!`到`upcase!`，结果将是:

`My name is Damien and I am 26 years old. I live in Paris, FRANCE.`

### 切片

国家通常以前 2 或 3 个字母显示。这种*切片*的方法会很棒。这个方法需要一个或两个参数。第一个是开始切片的索引，第二个是想要切片的长度。所以，如果我想要前两个字母，我将使用`slice(0, 2)`，这将返回前两个字母。如果我想要第三个字母的三个字母，我会用`slice(2, 3)`。记住我们从 0 开始计数！

```
# Modify the country variable
print "Country? "
country = gets.chomp.upcase!.slice(2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

现在，结果将是:

```
➜  ruby git:(master) ✗ ruby index.rb
First Name? damien
Age? 26
City? paris
Country? france
My name is Damien and I am 26 years old. I live in Paris, FR. 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

本文展示了如何使用 Ruby 要求用户给出提示。我们还看到了一些操作字符串的方法(slice，upcase，capitalize)。希望够清楚。

玩得开心！