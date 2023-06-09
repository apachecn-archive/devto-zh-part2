# Go 中的反射:创建堆栈[T]

> 原文：<https://dev.to/dannypsnl/reflection-in-go-create-a-stackt-1m3p>

你知道 Go 的包`reflect`能做什么吗？

不管你用不用它。理解它并不是一件坏事。

一个众所周知的事情是不要用泛型，我不会告诉你我们有泛型，我会告诉你一些基本的技巧来得到像泛型一样的结果。

真实世界示例:[elz-lang/collection/stack](https://github.com/elz-lang/elz/blob/master/collection/stack/stack.go)

Elz 是我正在开发的一种语言，但这不是重点。重点是这个集合/栈使用了我要讲的技巧。

看一看`Stack`型

```
type Stack struct {
    stack []interface{}
    limitT *reflect.Type
} 
```

Enter fullscreen mode Exit fullscreen mode

`limitT`是一个`*reflect.Type`，之所以它是一个指向`reflect.Type`而不是`reflect.Type`的指针，是因为我们可能没有指定它。

我们通过调用`WithT`来添加`Stack<T>`。

```
func (s *Stack) WithT(v interface{}) *Stack {
    t := reflect.TypeOf(v).Elem()
    s.limitT = &t
    return s
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么是`reflect.TypeOf(v).Elem()`？因为我们不能真正得到一个类型是接口的实例！相反，我们可以得到一个类型是一个接口的指针！

我们常见的习语是使用`(*SomeInterface)(nil)`来获取指向接口实例的指针。

现在我们知道用户代码可以是

```
type AST interface {
    Gen() llvm.Value
}

// main
s := stack.New().WithT((*AST)(nil)) 
```

Enter fullscreen mode Exit fullscreen mode

在我们这样做之后，用户不能推一个不实现`AST`的值。

那么，我们该怎么做呢？我们在`Push`
进行检查

```
func (s *Stack) Push(element interface{}) {
    if s.limitT != nil {
        if !reflect.TypeOf(element).Implements(*s.limitT) {
            panic(fmt.Sprintf("element must implement type: %s", *s.limitT))
        }
    }
    s.stack = append(s.stack, element)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`limitT`不是`nil`，表示我们不限制类型，只是继续下去。

但是如果我们限制类型，我们检查`element`是否实现了`limitT`。

如果没有，我们会扰乱这个过程。

现在我们有了一个可以保证运行时类型安全的堆栈。