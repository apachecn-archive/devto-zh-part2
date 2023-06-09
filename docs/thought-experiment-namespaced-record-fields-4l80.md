# 思维实验:命名记录字段

> 原文：<https://dev.to/robinheghan/thought-experiment-namespaced-record-fields-4l80>

我不喜欢在榆树窝唱片。这没什么大不了的，但它似乎总是导致比最初想象的更多的噪音。部分原因是 Elm 没有便于嵌套更新的语法。看看这个记录定义:

```
type alias Person =
  { name : Int
  , age : Int
  , pet : Pet
  }

type alias Pet =
  { name : String
  , age : Int
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我有一个人的记录，并想给宠物重新命名，代码应该是这样的:

```
-- Can't do this
{ person.pet | name = "Fido" }

-- Or this
{ person | pet = { person.pet | name = "Fido" } }

-- It has to be this
let
  pet = person.pet
in
{ person | pet = { pet | name = "Fido" } } 
```

Enter fullscreen mode Exit fullscreen mode

正如我之前说过的，这没什么大不了的，但它确实让我心痒痒的。一种方法是避免嵌套记录:

```
type alias PersonWithPet =
  { personName : String
  , personAge : Int
  , petName : String
  , petAge : Int
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用可扩展的记录语法:
来实现这种缩放

```
type alias Pet a =
  { a |
    petName : String
  , petAge : Int
  }

{- Works with PersonWithPet -}
renamePet : String -> Pet a -> Pet a
renamePet name pet =
  { pet | petName = name } 
```

Enter fullscreen mode Exit fullscreen mode

因此，这实际上解决了问题，但需要我在记录中的所有字段前加前缀。如果编译器支持让它变得更好呢？

让我们稍微转换一下话题，谈谈我之前选择的语言 Clojure。Clojure 是一个 Lisp，是动态类型的。代替记录，人们简单地使用地图(在 Elm 中我们称之为`Dict`)将数据分组在一起。Clojure 有自己的类型作为映射中的键，称为 keywords。他们长这样:

```
:name  ;; keyword  ;; Person with a Pet  (def  person  {  :name  "Robin"  :age  29  :pet  {  :name  "Fido"  :age  4  }  }  ) 
```

Enter fullscreen mode Exit fullscreen mode

在 Clojure 中，嵌套更新非常简单。如果我想用上面的人物定义给宠物重新命名，我会这样做:

```
(assoc-in  person  [:pet  :name]  "Baldur") 
```

Enter fullscreen mode Exit fullscreen mode

然而，有时避免嵌套是很有意义的，Clojure 对此有很好的支持:

```
(def  person-with-pet  {  :person/name  "Robin"  :person/age  29  :pet/name  "Fido"  :pet/age  4  }  ) 
```

Enter fullscreen mode Exit fullscreen mode

但这仍然要求我们在所有内容前加上前缀。这就是命名空间关键字发挥作用的地方:

```
(ns  person)  ;; namespace is set to person  ;; This equals our previous definition  (def  person-with-pet  {  ::name  "Robin"  ;; notice the double colon  ::age  29  :pet/name  "Fido"  :pet/age  4  }  )  ;; This is also the same thing  (def  person-with-pet  #:person{  :name  "Robin"  :age  29  :pet/name  "Fido"  :pet/age  4  }  ) 
```

Enter fullscreen mode Exit fullscreen mode

上面例子中的双冒号将填充当前名称空间作为关键字的前缀。这在 Elm 中可能会是什么样子？

```
module Pet exposing (Pet)

type alias Pet a =
  { a |
    :name : String ;; expands to pet/name
    :age : Int ;; expands to pet/age
  }

module Person

import Pet as P

type alias PersonWithPet =
  { :name : String -- person/name
  , :age : Int -- person/age
  , :P/name : String -- pet/name
  , :P/age : Int -- pet/age
  }

{- Works in PersonWithPet -}
renamePet : String -> P.Pet a -> P.Pet a
renamePet name pet =
  { pet | :P/name = name } 
```

Enter fullscreen mode Exit fullscreen mode

这是一种进步吗？也许吧。为嵌套更新找到一个好的语法可能更好，但是我不介意只有一个简单的语法来处理平面记录。