# 重新学习 Haskell(第 1 部分:基础)

> 原文：<https://dev.to/awwsmm/relearn-you-a-haskell-part-1-the-basics-i9l>

我通过自己的努力为你学了一个 Haskell！大约六个月前，从那以后我就没怎么和 Haskell 共事过。所以我当然什么都忘了。因此，我又重新研究了一遍，并制作了这本方便的复习指南(主要是为我自己，但也是为了让你快速了解 Haskell-land 的工作方式:

## `not`

Haskell 不使用`~`或`!`来表示否定，而是使用`not`函数:

```
ghci> not True -- comments come after two hyphens
False 
```

Enter fullscreen mode Exit fullscreen mode

...但是它确实用`/=`来表示“不等于”:

```
ghci> 5 /= 4
True 
```

Enter fullscreen mode Exit fullscreen mode

## 功能应用

Haskell 不使用括号来分隔函数名和它的参数；相反，我们只是使用空格:

```
ghci> max 3 4
4 
```

Enter fullscreen mode Exit fullscreen mode

## 部分功能应用

部分应用的函数可以保存在变量中供以后使用！

```
ghci> min5 = min 5

ghci> min5 4
4

ghci> min5 6
5 
```

Enter fullscreen mode Exit fullscreen mode

## 中缀符号

有些函数出现在两个参数之间比出现在两个参数之前更有意义。例如:

```
ghci> div 100 5 -- prefix notation
20

ghci> 100 `div` 5 -- infix notation
20 
```

Enter fullscreen mode Exit fullscreen mode

## 功能

通过将命名参数放在`=`之前并将函数定义放在
之后来定义基本函数

```
ghci> pythagoras a b = sqrt (a*a + b*b)

ghci> pythagoras 5 12
13.0 
```

Enter fullscreen mode Exit fullscreen mode

注意，函数名可以包含`'`(通常用于表示其他懒惰函数的严格版本)，并且不能以大写字母开头。

## 列表

在 Haskell 中，列表是同构的，字符串是字符列表:

```
ghci> ll = [1,2,3,4]

ghci> ss = ['e','l','l','o'] 
```

Enter fullscreen mode Exit fullscreen mode

单个元素可以(快速)添加到带有`:`的列表中，一个列表可以(慢速)添加到带有`++` :
的列表中

```
ghci> 'h':ss
"hello"

ghci> ll ++ [5] ++ [6,7]
[1,2,3,4,5,6,7]

ghci> 's':'m':ss -- : and ++ can be chained like this
"smello" 
```

Enter fullscreen mode Exit fullscreen mode

可以用`!!`操作符提取元素(不像 C 语言中的`[]`操作符):

```
ghci> ll !! 2 -- equivalent to ll[2] in C
3 
```

Enter fullscreen mode Exit fullscreen mode

列表本身可以包含项目列表或项目列表的列表，所有这些列表可以是不同的长度，只要它们都包含相同类型的对象(数字或字符，但不是两者都有)。

最后，列表按字典顺序进行比较，这意味着首先比较第一个元素，然后是第二个元素，依此类推:

```
ghci> [1, 2, 3] > [0, 4, 5]
True

ghci> [1, 2, 3] > [1, 2]
True 
```

Enter fullscreen mode Exit fullscreen mode

## 列表功能

### `head` / `tail` / `init` / `last` / `length`

```
ghci> ll
[1,2,3,4]

ghci> head ll -- first element of list
1

ghci> tail ll -- all but first element of list
[2,3,4]

ghci> init ll -- all but last element of list
[1,2,3]

ghci> last ll -- last element of list
4

ghci> length ll -- length of list
4 
```

Enter fullscreen mode Exit fullscreen mode

注意，`head`、`tail`、`init`和`last`如果在空列表中被调用，都会抛出错误，但是`length`返回 0。您可以通过`null` :
检查列表是否为空

```
ghci> length [] -- length of an empty list is zero
0

ghci> length ll -- ll has 4 elements in it at indices 0..3
4

ghci> null [] -- an empty list is null
True

ghci> null ll
False 
```

Enter fullscreen mode Exit fullscreen mode

### `reverse`

```
ghci> reverse ll -- reverse a list
[4,3,2,1] 
```

Enter fullscreen mode Exit fullscreen mode

### [T3`take`/`drop`](#-raw-take-endraw-raw-drop-endraw-)

```
ghci> take 1 ll -- take 1 element from the beginning of the list
[1]

ghci> take 3 ll -- take 3 elements
[1,2,3]

ghci> drop 1 ll -- drop 1 element from the beginning of the list
[2,3,4]

ghci> drop 3 ll -- drop 3 elements
[4] 
```

Enter fullscreen mode Exit fullscreen mode

### [T3`maximum`/`minimum`](#-raw-maximum-endraw-raw-minimum-endraw-)

```
ghci> maximum ll
4

ghci> minimum ll
1 
```

Enter fullscreen mode Exit fullscreen mode

### [T3`sum`/`product`](#-raw-sum-endraw-raw-product-endraw-)

```
ghci> sum ll -- 1 + 2 + 3 + 4
10

ghci> product ll -- 1 * 2 * 3 * 4
24 
```

Enter fullscreen mode Exit fullscreen mode

### `elem`

`elem`的工作方式有点像`contains()`在其他语言中的工作方式，如果第一个参数是第二个参数的元素，则返回 true，第二个参数必须是列表:

```
ghci> elem 2 ll
True

ghci> 5 `elem` ll
False 
```

Enter fullscreen mode Exit fullscreen mode

### `cycle` / `repeat` / `replicate`

用`cycle`重复一个列表；用`repeat`
重复单个对象

```
ghci> take 10 (cycle ll)
[1,2,3,4,1,2,3,4,1,2]

ghci> take 10 (repeat 5)
[5,5,5,5,5,5,5,5,5,5]

ghci> take 5 (repeat ll)
[[1,2,3,4],[1,2,3,4],[1,2,3,4],[1,2,3,4],[1,2,3,4]]

ghci> replicate 10 5 -- same as take 10 (repeat 5)
[5,5,5,5,5,5,5,5,5,5] 
```

Enter fullscreen mode Exit fullscreen mode

## 范围

范围使用整数和字符:

```
ghci> [1..10]
[1,2,3,4,5,6,7,8,9,10]

ghci> ['a'..'j']
"abcdefghij" 
```

Enter fullscreen mode Exit fullscreen mode

列表的前两个元素可以用来定义一个模式:

```
ghci> ['z','w'..'a']
"zwtqnkheb"

ghci> [26,23..1]
[26,23,20,17,14,11,8,5,2] 
```

Enter fullscreen mode Exit fullscreen mode

上面，列表在最后一个元素被点击时结束，但是你也可以通过使用`take` :
来定义你想要多少个元素

```
ghci> take 10 [1,3..]
[1,3,5,7,9,11,13,15,17,19] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*接下来的第 2 部分:列表理解、元组和类型类*