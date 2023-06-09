# Go-tcha:当通过指针赋值改变类型时

> 原文：<https://dev.to/pauljlucas/go-tcha-when-assigning-via-pointer-changes-your-type-2b3g>

我目前正在移植我的 [CHSM](http://chsm.sourceforge.net) (并发层次有限状态机)项目，该项目还有 C++和 Java 实现要做。移植既包括重写 Go 中的运行时库，也包括扩充 CHSM 编译器以发出 Go 代码。

> 简而言之，CHSM 就像一个普通的有限状态机:*状态*可以通过接收*事件*来进入和退出，这些事件导致状态之间的*转换*。与普通的有限状态机不同，CHSM 可以有一些*父*状态，其中嵌套有*子*状态:进入一个父状态又会进入它的一个子状态。当一个状态已经进入但尚未退出时，它被认为是*激活*。

为了强调本文的观点，这里给出的实现是真实情况的简化版本。例如，让我们将`State`实现为:

```
type State interface {
    Enter()
    Exit()
    Name() string
}

type CoreState struct {
    name   string
    parent Parent
}

func (s *CoreState) Enter() {
    fmt.Printf("CoreState.Enter(%s)\n", s.Name())
    if s.parent != nil {
        s.parent.switchActiveChildTo(s)
    }
}

func (s *CoreState) Exit() {
    fmt.Printf("CoreState.Exit(%s)\n", s.Name())
}

func (s *CoreState) Name() string {
    return s.name
} 
```

Enter fullscreen mode Exit fullscreen mode

这很简单。接下来，让我们将`Parent`实现为:

```
type Parent interface {
    State

    switchActiveChildTo(State)
}

type CoreParent struct {
    CoreState

    children    []State           // A parent has zero or more children.
    activeChild State             // One child is the active one.
}

func (p *CoreParent) Enter() {
    fmt.Printf("CoreParent.Enter(%s)\n", p.Name())
    p.CoreState.Enter()           // Enter ourselves first.
    if p.activeChild == nil {     // Default to first child.
        p.activeChild = p.children[0]
    }
    p.activeChild.Enter()         // Enter activeChild.
}

func (p *CoreParent) Exit() {
    p.activeChild.Exit()          // Exit activeChild first.
    p.CoreState.Exit()            // Then exit ourselves.
    fmt.Printf("CoreParent.Exit(%s)\n", p.Name())
}

func (p *CoreParent) switchActiveChildTo(child State) {
    p.activeChild = child
} 
```

Enter fullscreen mode Exit fullscreen mode

> CHSMs 的语义包括绕过父状态直接进入子状态的能力。如果发生这种情况，并且父节点已经是活动的，那么子节点必须请求父节点将其活动的子节点状态切换到自己，这就是`switchActiveChildTo()`方法。

### 问题

让我们写一个测试程序:

```
func main() {
    outer := &CoreParent{ CoreState: CoreState{ name: "outer" } }
    inner := &CoreParent{ CoreState: CoreState{ name: "inner" } }
    s := &CoreState{ name: "s" }

    // Normally, the CHSM compiler emits code to set the parent/child pointers.
    outer.children = []State{ inner }
    inner.parent = outer
    inner.children = []State{ s }
    s.parent = inner

    outer.Enter()
    fmt.Println("-------------------------")
    outer.Exit()
} 
```

Enter fullscreen mode Exit fullscreen mode

运行时，它打印:

```
CoreParent.Enter(outer)
CoreState.Enter(outer)
CoreParent.Enter(inner)
CoreState.Enter(inner)
CoreState.Enter(S)
-------------------------
CoreState.Exit(inner)
CoreState.Exit(outer)
CoreParent.Exit(outer) 
```

Enter fullscreen mode Exit fullscreen mode

那是不对的！对`Exit()` *的调用应该*与对`Enter()`的调用配对，但是`CoreState.Exit(s)`和`CoreParent.Exit(inner)`丢失了。为什么？经过几个小时的调试，我找到了这个:

```
func (s *CoreState) Enter() {
    // ...
        s.parent.switchActiveChildTo(s)
    // ...
}

func (p *CoreParent) switchActiveChildTo(child State) {
    p.activeChild = child
} 
```

Enter fullscreen mode Exit fullscreen mode

但这有什么错呢？子进程告诉它的父进程将它的活动子进程设置为它自己。这怎么可能*错*？

### 界面上的背景

在 Go 中，获得多态性的唯一途径是通过接口。Go 中的一个普通变量有两个属性:它的*静态类型*和它的*值*。静态类型是变量被声明为的类型。一个接口另外有一个*动态类型*，它是(指针)值所指向的对象的类型:

```
// static type = int; value = 42
var n int = 42

// static type = State; dynamic type = CoreParent; value = &outer
var i State = &outer 
```

Enter fullscreen mode Exit fullscreen mode

当您在接口上调用方法时，实现实际上是在接口的指针值所指向的对象上调用相应的方法。指针值成为方法接收者的值；接口的动态类型变成了接收者的*静态类型*。这意味着一旦接口值“衰减”成指针，多态性就消失了。

### Bug 的原因

当测试程序运行时，会发生以下情况。左侧显示了接收者的名称、静态类型和值:

```
1\. p *CoreParent = &outer           | CoreParent.Enter(outer)
2\. s *CoreState  = &outer.CoreState |   CoreState.Enter(outer)
3\. p *CoreParent = &inner           |     CoreParent.Enter(inner)
4\. s *CoreState  = &inner.CoreState |       CoreState.Enter(inner)
5\. p *CoreParent = &outer           |         switchActiveChildTo(inner.CoreState) 
```

Enter fullscreen mode Exit fullscreen mode

此时，应该是`*CoreParent`类型的`&inner`的`outer.activeChild`实际上被设置为`*CoreState`类型的`&inner.CoreState`。令人困惑的是这两个对象有相同的名字(因为嵌入类型`CoreState`的`Name()`是从`CoreParent`转发过来的)。

当调用`Exit(outer)`时，会发生以下情况:

```
1\. p *CoreParent = &outer           | CoreParent.Exit(outer)
2\. s *CoreState  = &outer.CoreState |   CoreState.Exit(outer)
3\. s *CoreState  = &inner.CoreState |     CoreState.Exit(inner) 
```

Enter fullscreen mode Exit fullscreen mode

`CoreState.Exit(s)`从未被调用，因为`CoreParent.Exit(inner)`从未被调用，而是被调用了`CoreState.Exit(inner)`。为了更清楚地说明这一点，我们可以增加`CoreParent.Enter()` :

```
func (p *CoreParent) Enter() {
    // ...
    t0 := reflect.TypeOf(p.activeChild)
    p.activeChild.Enter()         // eventually calls switchActiveChildTo()
    t1 := reflect.TypeOf(p.activeChild)
    if t1 != t0 {
        fmt.Printf("%s type changed: %v != %v\n", p.activeChild.Name(), t1, t0)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且它额外打印:

```
inner type changed: *CoreState != *CoreParent 
```

Enter fullscreen mode Exit fullscreen mode

### [严格来说...](#strictly-speaking)

为了防止 Go 社区中的纯粹主义者拿着干草叉来找我，我将指出`activeChild`的类型实际上是*而不是*改变了。实际上*发生的是`activeChild`被分配了一个指向*不同的*对象的指针:嵌入在`CoreParent`内部的`CoreState`对象——所以*当然*类型改变了。*

嗯，是的，但是:如果你从赋值前后的接口中提取指针值并进行比较比如:

```
func getPtrValue(i interface{}) uintptr {
    return (*[2]uintptr)(unsafe.Pointer(&i))[1]
}

func (p *CoreParent) Enter() {
    // ...
    a0 := getPtrValue(p.activeChild)
    p.activeChild.Enter()
    a1 := getPtrValue(p.activeChild)
    if a1 != a0 {
        fmt.Printf("%s address changed: %v != %v\n", p.activeChild.Name(), a1, a0)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

程序不*不*打印`inner address changed`，因为它*不*。内存中的一个`CoreParent`对象在零偏移量处嵌入了`CoreState`。所以，是的，`activeChild`被分配了一个指针，指向具有不同类型的“不同”对象——但是该对象具有相同的地址。

没有区别的区别？

### 修罗

回到 CHSM 的 Go port:如何将运行时库更改为我想要的样子？我解决这个问题的方法是改变`CoreState.Enter()`，这样它就可以在一个包含指向所有状态的指针的切片中使用一个新的`CoreState.id`字段来查找指向原始状态(原始类型)的对象的指针:

```
var allStates []State

func (s *CoreState) Enter() {
    fmt.Printf("CoreState.Enter(%s)\n", s.Name())
    if s.parent != nil {
        child := allStates[s.id]
        s.parent.switchActiveChildTo(child)
    }
}

// ...

func main() {
    outer := &CoreParent{ CoreState: CoreState{ name: "outer", id: 0 } }
    inner := &CoreParent{ CoreState: CoreState{ name: "inner", id: 1 } }
    s := &CoreState{ name: "s", id: 2 }

    allStates = []State{ outer, inner, s }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode