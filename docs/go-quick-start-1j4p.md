# 快速启动

> 原文：<https://dev.to/dannypsnl/go-quick-start-1j4p>

我将向你展示足够的围棋入门知识。你准备好了吗？

## 变量

在围棋中，我们有`const`、`var`、&短宣&定义。

在 Go 程序的顶层范围内(超出功能)

```
const C = "constant"
var V = "var" 
```

Enter fullscreen mode Exit fullscreen mode

当我们有很多变量时，我们可以用一种懒惰的方式来做这件事(p.s. `var`作为`const` )

```
const (
    C = 1
    C1 = 2
    C2 = 3
) 
```

Enter fullscreen mode Exit fullscreen mode

在`func` (Go 的函数关键字)中，我们可以有`const`、`var`、&的简称。

```
func main() {
    a := 0 // short
    var b = 1
    const c = 2
} 
```

Enter fullscreen mode Exit fullscreen mode

关于短格式重要的一点是它只能有`variableName := value`，不能有更多

我们也可以在变量名后面加上类型，例如`var a int = 1`。但我们通常不会这样做。

一个重要的概念是我们不能有任何变量在 Go 中未被使用。它们会导致编译错误。

## 功能

我们使用`func`来开始一个函数定义。

```
func foo() {} 
```

Enter fullscreen mode Exit fullscreen mode

函数定义的格式为:`func funcName(parameterList) returnType { body }`

很难用描述来说明它是如何工作的，所以让我们看一个例子。

```
func add(lv, rv int) int {
    return lv + rv
}
// Use
a := add(1, 2)
// a == 3 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，如果参数具有相同的类型，我们可以省略类型，我们使用最后一个作为 rest 类型。

重要的是我们可以在一个函数中返回几个类型。

```
func Lookup(name string) (age int, error) {
    age, ok := aMap[name]
    if !ok {
        return 0, fmt.Errorf("do not have this user: %s", name)
    }
    return age, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

而这里展示了一个有趣的代码:`age int`，是的，我们可以给返回值一个名字。这样我们就可以给它赋值&显示它是什么。

## 结构

使用真实世界例子可能更好。所以这里我们为用户
创建一个类型

```
type User struct {
    Name string
    Age int
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，也许你已经开始思考 OOP 了，但不是，Go 不是 OOP 语言，为什么？让我展示给你看。

```
func (u *User) SetName(newName string) {
    u.Name = newName
}

func (u User) GetName() string {
    return u.Name
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们在 Go 中创建`user.Method()`的方式。关注`receiver`(我们称`u *User`为这部分接收者)。在`GetName`中，如果你尝试修改`u.Name`，是的，可以，但是不会影响你用来调用`GetName`的对象的`Name`。

那为什么`SetName`能做得那么成功呢？因为它使用**指针接收器**！所以现在我可以告诉你，这只是一个语法糖像:

```
char* user_get_name(struct user u) {
  return u.name;
}
// In use
struct user *u = &(struct user) {
  .name = "Danny",
  .age = 21
};
char* name = user_get_name(*u); 
```

Enter fullscreen mode Exit fullscreen mode

## 收藏

我们都知道 Go 没有泛型，但有时泛型是有用的，所以这就是为什么 Go 中有一些内置的集合可以处理不同的类型。

*   地图
*   阵列/切片

图为:

```
m := map[string]int {
    "Danny": 21,
    "Ben": 40,
} 
```

Enter fullscreen mode Exit fullscreen mode

格式是`map[type]type`，你可以在那里选择任何类型。

数组和切片非常相似，但又不同，这不是我的观点，所以我不会谈论它。格式为`[]type`，作为地图，可以更改类型部分。

## 界面

界面是围棋中非常重要的一部分。因为这个模型帮助我们在没有继承系统的情况下工作，比如`Java`或者`C++`。

界面看起来像:

```
type Stringer interface {
    String() string
} 
```

Enter fullscreen mode Exit fullscreen mode

我们只是在那里留下了一个函数类型。等等，怎么用？不用担心，任何类型拥有一个函数`String() string`就是类型`Stringer`。

事情就是这样的。所以，我们甚至可以这样做:

```
type UserDatas map[string]int // map of name to age
func (udatas UserDatas) String() string {
    // ignore
} 
```

Enter fullscreen mode Exit fullscreen mode

## 戈鲁丁&频道

在 Go 中，我们使用 goroutine & channel 来控制我们的并发系统。

什么是 goroutine？goroutine 是一个运行某些任务的例程，由 Go runtime 管理。如何创建一个？

```
go func() {
    fmt.Println("Hi")
}() 
```

Enter fullscreen mode Exit fullscreen mode

我们在函数调用中使用关键字`go`来启动一个函数。谁破坏了它？运行时。所以这就是为什么我们不需要担心它的资源。

但是我们如何在它完成任务后得到数据呢？这就是我们有**频道**的原因。

```
// main
result := make(chan int)
go func() {
    for i:=0; i<10; i++ {
        result <- i*2 // write into channel
    }
}()
// range over channel
for elem := range result {
    fmt.Println(elem)
} 
```

Enter fullscreen mode Exit fullscreen mode

流量喜欢

```
main
 | \
 | | for 0..9, result <- i*2
 | / f1 done
 |
 | read from channel
 |
main done 
```

Enter fullscreen mode Exit fullscreen mode

通道范围和写入通道概念上是同时运行的。这就是 Go 中并发的样子。

## 主

在 Go 中，一个可执行文件需要两件事。

*   包装内`main`
*   `func main()`最简单的文件是:

```
package main

func main() {} 
```

Enter fullscreen mode Exit fullscreen mode

## 包

最后，我们谈谈包装。
在 Go 中，只打包同一个目录下的一些文件，用相同的包名。
然后我们使用环境变量`GOPATH`来搜索它。

进口是什么样子的？

```
import "github.com/dannypsnl/rocket" 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们在`$GOPATH/src/github.com/dannypsnl/rocket`下导入包。当然，我们那里没有，所以现在我们可以执行一个命令:`go get ./...`。该命令将尝试解决我们需要的依赖性。所以它会把`github.com/dannypsnl/rocket`下载到你的 GOPATH 中

这是围棋的快速入门。感谢阅读，拜拜。