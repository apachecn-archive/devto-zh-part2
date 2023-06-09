# 继续-清空 Go 中的接口

> 原文：<https://dev.to/shearytan/continue---empty-interfaces-in-go-219e>

# 清空界面

顾名思义，它不包含任何方法。当我们想要存储不同类型的数据时，这很有用。

从之前的[帖子](https://dev.to/shearywtan/brief-introduction-to-interfaces-in-go-34dg)来看，我们只使用数字作为例子。如果我们想要包含不同类型的数据呢？

例如:

```
package main

import "fmt"

type animal struct {
    age   int
    color string
    sound string
}

type cat struct {
    animal
    bossy bool
}

type dog struct {
    animal
    friendly bool
}

func main() {
    cassy := cat{animal{3, "white", "meow"}, true}
    kitty := cat{animal{2, "black", "meow"}, true}
    missy := cat{animal{1, "brown", "meow"}, true}
    cats := []cat{cassy, kitty, missy}

    rocky := dog{animal{4, "brown", "woof"}, true}
    lucy := dog{animal{3, "white", "woof"}, true}
    tucker := dog{animal{2, "black", "woof"}, true}
    dogs := []dog{rocky, lucy, tucker}

    for i, val := range cats {
        fmt.Println(i, " - ", val)
    }

    for i, val := range dogs {
        fmt.Println(i, " - ", val)
    }
}

// 0  -  {{3 white meow} true}
// 1  -  {{2 black meow} true}
// 2  -  {{1 brown meow} true}
// 0  -  {{4 brown woof} true}
// 1  -  {{3 white woof} true}
// 2  -  {{2 black woof} true} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的代码，所以我不认为我需要在这里解释...好吧..所以我们这里有两种类型`dog()`和`cat()`，一群狗和猫，还有两个 for 循环通过`dogs`和`cats`，最后打印出狗和猫。

> *看到这里的问题了吗？和上一篇文章一样，为什么我们需要写两个不同的函数(在这里是 for 循环)来做同样的事情？*

但与上一个帖子不同的是，我们这里有不同的类型:`int`、`string`、`type`中的`bool`。

相反，我们为什么不包括我们的朋友接口:

```
package main

import "fmt" 
```

Enter fullscreen mode Exit fullscreen mode

```
type animals interface{} 
```

Enter fullscreen mode Exit fullscreen mode

```
type animal struct {
    age   int
    color string
    sound string
}

type cat struct {
    animal
    bossy bool
}

type dog struct {
    animal
    friendly bool
} 
```

Enter fullscreen mode Exit fullscreen mode

```
func main() {
    cassy := cat{animal{3, "white", "meow"}, true}
    kitty := cat{animal{2, "black", "meow"}, true}
    missy := cat{animal{1, "brown", "meow"}, true}

    rocky := dog{animal{4, "brown", "woof"}, true}
    lucy := dog{animal{3, "white", "woof"}, true}
    tucker := dog{animal{2, "black", "woof"}, true}

    friends := []animals{cassy, kitty, missy, rocky, lucy, tucker}

    for i, val := range friends {
        fmt.Println(i, " - ", val)
    }
}

// 0  -  {{3 white meow} true}
// 1  -  {{2 black meow} true}
// 2  -  {{1 brown meow} true}
// 3  -  {{4 brown woof} true}
// 4  -  {{3 white woof} true}
// 5  -  {{2 black woof} true} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个空的动物接口，回想一下'*'的语句，当我们想要存储不同类型的数据时，这很有用。*'，对于类型`cat`和`dog`，我们肯定有一堆不同的类型。

很难具体说明我们到底需要哪种类型，所以我们为什么不把所有东西都扔进接受所有东西的东西(空接口)中呢？

或者你也可以这样做:

## 空接口为 param

```
package main

import (
    "fmt"
)

type animal struct {
    age   int
    color string
    sound string
}

type cat struct {
    animal
    bossy bool
}

type dog struct {
    animal
    friendly bool
} 
```

Enter fullscreen mode Exit fullscreen mode

```
func result(a interface{}) { // Here!
    fmt.Println(a)
}

func main() {
    cassy := cat{animal{3, "white", "meow"}, true}
    rocky := dog{animal{4, "brown", "woof"}, true}

    result(cassy)
    result(rocky)
}

// {{3 white meow} true}
// {{4 brown woof} true} 
```

Enter fullscreen mode Exit fullscreen mode

这里我只包括了一只狗和一只猫，只是为了减少复杂性。

或者这个:

## 空界面为切片

```
package main

import "fmt"

type animal struct {
    age   int
    color string
    sound string
}

type cat struct {
    animal
    bossy bool
}

type dog struct {
    animal
    friendly bool
} 
```

Enter fullscreen mode Exit fullscreen mode

```
func main() {
    cassy := cat{animal{3, "white", "meow"}, true}
    kitty := cat{animal{2, "black", "meow"}, true}
    missy := cat{animal{1, "brown", "meow"}, true}

    rocky := dog{animal{4, "brown", "woof"}, true}
    lucy := dog{animal{3, "white", "woof"}, true}
    tucker := dog{animal{2, "black", "woof"}, true}

        // Here!
    friends := []interface{}{cassy, missy, kitty, rocky, lucy, tucker}

    fmt.Println(friends)
}

// [{{3 white meow} true} {{1 brown meow} true} {{2 black meow} true} {{4 brown woof} true} {{3 white woof} true} {{2 black woof} true}] 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个切片(数组)。

**接口的基本思想:防止调用方法时的重复。空界面？以防止在调用不同类型的方法时出现重复。**

> *感谢你阅读我的帖子，我仍然是一个新手地鼠，但正在努力成为一个合格的地鼠:3*