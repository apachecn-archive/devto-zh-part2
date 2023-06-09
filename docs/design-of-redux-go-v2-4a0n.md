# Redux-go v2 的设计

> 原文：<https://dev.to/dannypsnl/design-of-redux-go-v2-4a0n>

Redux 是一个单流状态管理器。去年我把它从 JS 移植到 Go。

但是那里有一样东西使我无法熟悉它，那就是国家的类型！

在 Redux 中，我们将存储与许多 reducers 结合在一起。然后，我们将动作分派到存储中来更新我们的状态。这意味着我们的国家可以是任何东西。

在 JS 中，我们有一个这样的减速器:

```
const counter = (state = 0, action) => {
    switch action.type {
    case "INC":
        return state + action.payload
    case "DEC":
        return state - action.payload
    default:
        return state
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错，因为我们这里没有类型限制。在 Redux-go v1 中，我们有:

```
func counter(state interface{}, action action.Action) interface{} {
    if state == nil {
        return 0
    }
    switch action.Type {
    case "INC":
        return state.(int) + action.Args["payload"].(int)
    case "DEC":
        return state.(int) - action.Args["payload"].(int)
    default:
        return state
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看看那些断言，当然是安全的，因为你应该知道你用的是哪种类型。但是太丑了。

所以我决定改变这一切。在 v2 中，我们有:

```
func counter(state int, payload int) int {
    return state + payload
} 
```

Enter fullscreen mode Exit fullscreen mode

等等，什么！！！？

所以我得解释一下背后的魔力。

首先是如何获得用户想要的状态类型。答案是`reflect`包。

但是怎么做呢？下面我们来挖掘一下`v2/store`功能:`New`。

```
func New(reducers ...interface{}) *Store 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们必须接受任何类型的参数部分。

那我们来看看类型:`Store`(仅核心部分)

```
type Store struct {
    reducers []reflect.Value
    state map[uintptr]reflect.Value
} 
```

Enter fullscreen mode Exit fullscreen mode

Yp，我们存储类型为`reflect.Value`的反射结果。

但是为什么呢？因为如果我们存储`interface{}`，每次想调用的时候都要调用`reflect.ValueOf`！那会变得太慢。

而`state`后面会有解释。

所以在`New`体内。

```
func New(reducers ...interface{}) *Store {
    // malloc a new store and point to it
    newStore := &Store{
        reducers: make([]reflect.Value, 0),
        state: make(map[uintptr]reflect.Value),
    }
    // range all reducers, of course
    for _, reducer := range reducers {
        r := reflect.ValueOf(reducer)
        checkReducer(r)
        // Stop for while
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，`checkReducer`是什么？现在就来看看吧！

```
func checkReducer(r reflect.Value) {
    // Ex. nil
    if r.Kind() == reflect.Invalid {
        panic("It's an invalid value")
    }

    // reducer :: (state, action) -> state

    // Missing state or action
    // Ex. func counter(s int) int
    if r.Type().NumIn() != 2 {
        panic("reducer should have state & action two parameter, not thing more")
    }
    // Return mutiple result, Redux won't know how to do with this
    // Ex. func counter(s int, p int) (int, error)
    if r.Type().NumOut() != 1 {
        panic("reducer should return state only")
    }
    // Return's type is not input type, Redux don't know how would you like to handle this
    // Ex. func counter(s int, p int) string
    if r.Type().In(0) != r.Type().Out(0) {
        panic("reducer should own state with the same type at any time, if you want have variant value, please using interface")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在回到`New`

```
// ...
for _, reducer := range reducers {
    // ...
    checkReducer(r)
    newStore.reducers = append(newStore.reducers, r)

    newStore.state[r.Pointer()] = r.Call(
        []reflect.Value{
            reflect.Zero(r.Type().In(0)),
            reflect.Zero(r.Type().In(1)),
        },
    )[0]
}
return newStore
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这就是`state`的工作方式，使用 reducer 的地址映射它的状态。

这个方法允许你从一个函数中调用一个`reflect.Value`。

它是签名所需的参数类型。它总是返回几个`reflect.Value`，但是由于我们只是非常确定我们只返回一个东西，所以我们可以只提取索引 0。

然后是`state`，为什么我这次选择使用指针而不是函数名？

想着这个:

```
// pkg a
func Reducer(s int, p int) int
// pkg b
func Reducer(s int, p int) int
// pkg main
func main() {
    store := store.New(a.Reducer, b.Reducer)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该选择哪一个？当然，我们可以试着留下包名使它能被识别。

但是接下来真的很难:

```
func main() {
    counter := func(s int, p int) int { return s + p }
    store := store.New(counter)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你认为计数器的名字是 counter，那就完全错了，它的名字是 **func1** 。

因此，我决定使用函数本身来获取映射状态。那就是新的 API: `StateOf`

```
func (s *Store) StateOf(reducer interface{}) interface{} {
    place := reflect.Valueof(reducer).Pointer()
    return s.state[place].Interface()
} 
```

Enter fullscreen mode Exit fullscreen mode

重点是`reflect.Value.Interface`，这个方法返回它所拥有的值。

我们在这里返回`interface{}`的原因是因为，我们没有办法转换成用户想要的类型，而用户总是知道他们实际上得到的是什么，只是为了方便我们让用户可以为他们的状态使用任何类型，所以他们不需要做`state.(int)`这些断言。

现在，你就这样工作:

```
func main() {
    counter := func(s int, payload int) int {
        return s + payload
    }
    store := store.New(counter)
    store.Dispatch(10)
    store.Dispatch(100)
    store.Dispatch(-30)
    fmt.Printf("%d\n", store.StateOf(counter)) // expected: 80
} 
```

Enter fullscreen mode Exit fullscreen mode

以上是对 v2 最大的突破，感谢阅读。