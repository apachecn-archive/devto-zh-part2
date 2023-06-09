# 1010 黑仔榆树提示像大师一样编码

> 原文：<https://dev.to/blackode/1010-killer-elm-tips-to-code-like-a-master-37g>

## 亲榆提示写代码像亲

[![designed using https://crello.com](img/79d5c37451ef696849649ea3e5a3c1c1.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--KNsHv3kK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2AV_Q37MuElfDww4uNDOCsSg.jpeg) *设计使用【https://crello.com】*

在生产中使用榆树涉及教与学。这篇文章包含了 10 个你可能知道也可能不知道的杀手职业榆树技巧。

## 0001 —键入别名作为函数

`type`别名可以用作构造新值的函数。

```
> type alias User = { name: String, age: Int }
> user = User "john" 23
{ name = "john", age = 23 } : Repl.User
> user
{ name = "john", age = 23 } : Repl.User 
```

Enter fullscreen mode Exit fullscreen mode

您只需按照类型别名定义的顺序传递初始值。

## 0010 —调用中缀函数

在`elm`中，几乎所有的东西都是`function`或者可以表现得像`function`。

`elm`中的加法运算只是使用`+`的函数调用。`+`的定义就像

```
(+) : number -> number -> number 
```

Enter fullscreen mode Exit fullscreen mode

`type`规范明确告诉你，它接受`two` `number`参数并返回一个`number`。因为是**中缀**函数，所以调用这个函数的方式不同。

**中缀函数**是其*名*在参数之间的函数。左侧参数作为第一个参数传递，右侧参数作为第二个参数传递。

### 举例:1+2

在上面的例子中，`+`是函数名，`1,2`是它的参数，其中`1`是第一个参数，`2`是第二个参数。

也可以像`(+)`一样将函数名括在括号`()`内来调用中缀函数。

```
> (+) 1 2
3 : number

-- which is the same as calling 
> 1 + 2
3: number 
```

Enter fullscreen mode Exit fullscreen mode

## 0011 —功能组成

如你所知`elm`无处不在`functions`。您可以使用> >
将功能组合成另一个功能

```
> wordCount = String.words >> List.length
<function> : String -> Int

> wordCount " Medium is full of knowledge resources"
6 : Int 
```

Enter fullscreen mode Exit fullscreen mode

`wordCount`函数将字符串作为输入并返回`Int`。传递给`String.words`的字符串值再传递给`List.length`，后者给出`Int`值。

这非常有用。

[![type distribution](img/04a94fa3b09ede3dd4ee057da6bb1677.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--dVJVV4wS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2AJvpMlEk4pk--ksg-ym24fA.png) *类型分布*

## 0100 —积分免费

这种无点式编码是在函数体中省略一个或多个参数。

要理解这个概念，请看下面的例子。

在这里，我试图编写一个名为 double 的函数，它将给定值加倍。

```
> double num = num * 2
<function> : number -> number

> double 5
10 : number 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您可以省略参数 num

你可以像
一样变魔术

```
> double = (*) 2
<function> : number -> number

> double 5
10 : number

> plus = (+)
<function> : number -> number -> number

> plus 5 5
10 : number 
```

Enter fullscreen mode Exit fullscreen mode

## 0101 —创建元组的特殊功能

在 elm 中，可以使用`(,)`。这是一个用于创建元组的特殊函数。神奇的是，它允许你在`()`中使用尽可能多的`,`。

例如，`(,,)`构建了一个三元组，而`(,,,)`构建了一个四元组。

查看以下示例，了解更多信息

```
> (,) "apple" "banana"
("apple","banana") : ( String, String )

> (,,) "apple" 12 "banana"
("apple",12,"banana") : ( String, number, String )

> (,,,) 1 2 3 4
(1,2,3,4) : ( number, number1, number2, number3 ) 
```

Enter fullscreen mode Exit fullscreen mode

对于 **N，**你必须给 **N+1 个**参数

您可以使用任意多的逗号。`,`将作为一个中缀函数，函数名在两个参数之间。

## 0110 —用于模式匹配的函数定义中的特殊符号

假设您有一个包含以下内容的记录**模型**

```
> model = {name = { firstName = "john", lastName = "ankanna"}, age = 23}

{ name = { firstName = "john", lastName = "ankanna" }, age = 23 }
    : { age : number, name : { firstName : String, lastName : String } } 
```

Enter fullscreen mode Exit fullscreen mode

如果你观察**模型**，它有嵌套的记录名`firstName`、`lastName`。

假设，您需要更新模型中的 firstName 字段，然后您可以通过如下参数进行模式匹配。

```
> updateFirstName ({name} as model)= {model | name = {name | firstName = "joe"}}

<function>
    : { c | name : { b | firstName : a } }
      -> { c | name : { b | firstName : String } } 
```

Enter fullscreen mode Exit fullscreen mode

`{name} as model`是这里匹配的图案。

[![pattern match](img/c2f98f9f969fef71d7971c4af52833ac.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--NxHhCABT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AzuXry4xsHLZkfG01gBVLiQ.png) *图案匹配*

现在，您只需将定义好的`model`传递给函数`updateFirstName`来更新`name`中的`firstName`字段。

```
>updateFirstName model
{ name = { firstName = "joe", lastName = "ankanna" }, age = 23 }
    : { age : number, name : { lastName : String, firstName : String } } 
```

Enter fullscreen mode Exit fullscreen mode

[![updated firstName](img/d71467d5d51edb19b58e506abfa150b0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--dbE3TKRs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2402/1%2Ag7oITqRACmi06XDWrJ72tw.png) *更新名字*

## 0111 —单位类型()

单元类型是只允许一个值的类型，因此不能保存任何信息。有时它也被称为 0-元组类型。

用`()`表示，其值与`()`相同。

```
> void = ()
() : ()  -- value : type are same here 
```

Enter fullscreen mode Exit fullscreen mode

读取[单元类型](https://en.wikipedia.org/wiki/Unit_type)

## 1000 — < |操作员

这个`<|`比函数表达式优先级低。

f(g(h(x)))可以写成 f

查看下面的例子

```
> add10 = (+) 10
<function> : number -> number

> mul10 = (*) 10
<function> : number -> number

> 10 |> mul10 |> add10
110 : number

> mul10 <| add10 <| 10
200 : number 
```

Enter fullscreen mode Exit fullscreen mode

让我们做一些疯狂的事情

```
>add = (+)
<function> : number -> number -> number

> 10 |> (add <| 10)
20 : number

> (10 |> add) <| 10
20 : number

> 10 |> add <| 10
-- SYNTAX PROBLEM -------------------------------------------- repl-temp-000.elm

Conflicting associativity for binary operators (|>, <|). Consider adding
parentheses to disambiguate. 
```

Enter fullscreen mode Exit fullscreen mode

## 1001-模式匹配至少具有元素的列表

如果你想匹配一个至少包含两个条目的列表，你可以利用`::`。

```
a :: b :: rest 
```

Enter fullscreen mode Exit fullscreen mode

***实现***

例如，如果列表中的项目少于 2 个，您必须返回[]，否则您必须返回列表的其余部分。

*   输入:**【1，2，3，4，5】**输出:**【3，4，5】**
*   输入:**【1】**输出:

```
import Html exposing (text)

rest list = 
  case list of
    a :: b :: rest -> --this matches list with at least two items
      rest
    _ -> 
      []

main =
  text <| toString <| rest [1,2,3,4,5] -- returns [3,4,5] 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/f5b1fa9ed3300482059e3b946b9cc6c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KEQNyJwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2148/1%2AwLEV_gzzVz0YCETOP_X50A.png)T3】

```
import Html exposing (text)

rest list = 
  case list of
    a :: b :: rest -> --this matches list with at least two items
      rest
    _ -> 
      []

main =
  text <| toString <| rest [1] -- returns [] 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/13ca1761a321452e17fae845a5461d1d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZiuoTr9E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2542/1%2Ab_7Cvax1TMFQQnd_K6NuCg.png)

## 1010 —相似= >榆树中的函数

```
> add1 = \n -> n +1
<function> : number -> number

> add1 5
6 : number 
```

Enter fullscreen mode Exit fullscreen mode

希望你喜欢整个背景。随意分享，让别人从你身上受益。

快乐的长编码生活:)**