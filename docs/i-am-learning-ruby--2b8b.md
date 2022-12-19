# 我正在学习红宝石

> 原文：<https://dev.to/sudhansubedi/i-am-learning-ruby--2b8b>

在开始使用 ruby on Rails 之前，我正在学习 Ruby 编程语言，我发现了以下关于 Ruby 的事实。我正在写今天学到的 ruby 编程基础。

## 1。可变的

*   Ruby 变量是动态类型和鸭类型
*   可以简单地定义赋值给变量

```
count = 10 
awesome_count = 20 // correct convention
awesomeCount = 20 // Wrong convention 
```

Enter fullscreen mode Exit fullscreen mode

*   耶！这里我们不需要任何分号
*   可变定义的通用约定是蛇的情况，而不是骆驼的情况
*   在代码中，通常使用 2 个空格而不是 4 个空格

## 2。用 Ruby 注释

*   用 ruby 编写注释非常简单直接
*   例如

```
# This is ruby comment 
```

Enter fullscreen mode Exit fullscreen mode

## 3。Ruby 中的函数

*   可以像这样使用“def”来定义函数

```
def double(value); value * 2; end
double(2) // results 4 
```

Enter fullscreen mode Exit fullscreen mode

*   我们也不需要 return 语句来返回值
*   在上面的例子中，单行函数和语句由分号分隔，请不要混淆
*   对于多行，我们可以这样写

```
def double(value)
 value * 2
end
double(2) // results 4 
```

Enter fullscreen mode Exit fullscreen mode