# redux-go v2.1 中的魔法:包重赛

> 原文：<https://dev.to/dannypsnl/magic-in-redux-go-v21-package-rematch-390e>

几天前，我发布了 redux-go v2.1

目的是:创建 reducer & action，然后管理它们之间的关系是相当困难的！

让我们从基础 v2 商店
开始

```
// package reducer
func Counter(state int, action string) int {
    switch action {
    case "INCREASE":
        return state + 1
    case "DECREASE":
        return state + 1
    default:
        return state
    }
}

// func main
store := store.New(reducer.Counter)
store.Dispatch("INCREASE")
store.StateOf(reducer.Counter) 
```

Enter fullscreen mode Exit fullscreen mode

当你有 30 个 reducers，每个包含 3 个动作，如何管理这个复合体？

在传统的方式中，我们遵循一个受限的命名规则。

例如:

```
// package reducer/counter
const (
    Increase = "REDUCER_COUNTER_INCREASE"
    Decrease = "REDUCER_COUNTER_DECREASE"
)

// package reducer
func Counter(state int, action string) int

// func main
store := store.New(reducer.Counter)
store.Dispatch(counter.Increase)
store.StateOf(reducer.Counter) 
```

Enter fullscreen mode Exit fullscreen mode

如何传播这些行为并不重要，关键是我们用手工来管理它们！而且手工造成不稳定！

这就是为什么我们需要包重赛。

它创造了一种更自然的方式来管理你的 reducer-action 关系。

```
// package reducer/todo
var Reducer *todoModel

func init() {
    Reducer = &todoModel{
        State: make([]Todo, 0),
    }
}

type Todo struct {
    Title string
    Done bool
}

type Model []Todo

type todoModel struct {
    rematch.Reducer
    State Model
}

func (todo *todoModel) AddTodo(state Model, title string) Model {
    return append(state, Todo{Title: title})
} 
```

Enter fullscreen mode Exit fullscreen mode

现在当我们使用它时，这种关系变得非常明显

```
// func main
store := store.New(todo.Reducer)
addTodo := todo.Reducer.Action(todo.Reducer.AddTodo)

store.Dispatch(addTodo.With("first todo"))
store.Dispatch(addTodo.With("second todo"))

store.StateOf(todo.Reducer) 
```

Enter fullscreen mode Exit fullscreen mode

与手动创建相比，它需要更多的代码，但也有更多的限制。

现在，让我们来看看是什么使这些发生的。

首先，我们从`store.New`(基于 2.1.1 版)
开始

```
// package store
func New(reducers ...interface{}) *Store {
    newStore := &Store{
        reducers: make(map[uintptr]reflect.Value),
        state: make(map[uintptr]reflect.Value),
    }
    // later
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个区别是`Store.reducers`，因为对于`rematch`，reducer 的地址不能映射到状态，我将在后面解释。

```
// func store.New
for _, reducer := range reducers {
    r := reflect.ValueOf(reducer)
    checkReducer(r)

    if _, ok := newStore.state[r.Pointer()]; ok {
        panic("You can't put duplicated reducer into the same store!")
    }

    actualReducer, initState := getReducerAndInitState(r)

    newStore.reducers[r.Pointer()] = actualReducer
    newStore.state[r.Pointer()] = initState
}
return newStore 
```

Enter fullscreen mode Exit fullscreen mode

我们还在检查减速器，让我们来查看一下

```
// func checkReducer, adding part
if r.Kind() == reflect.Ptr {
    v := reflect.Indirect(r) // dereference from ptr
    if v.FieldByName("State").Kind() == reflect.Invalid {
        panic("Reducer structure must contains field[State]")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们添加检查`Kind`是`Ptr`，因为`rematch.Reducer`将它的指针发送到存储中！

如果我们找不到字段`State`，我们就说 reducer 无效并惊慌失措(这是一个真正缺失的协议，但只有编写器需要担心，用户只需要知道他们必须创建这个字段)。所以我们可以保证在下面的流程中不用检查这些。

然后我们检查状态是否已经存在于存储中。如果答案是肯定的，我们就惊慌失措。

最后，我们必须得到初始状态和实际减速器，为什么它叫`actual reducer`？因为我们不能真正执行一个结构！减速器将在进程中执行是另一回事。它是由包重匹配创建的。所以让我们深入研究一下`getReducerAndInitState`这个函数，以理解它是如何工作的，以及为什么我们必须改变`Store.reducers`的类型。

```
// func getReducerAndInitState
if r.Kind() == reflect.Ptr {
    v := reflect.Indirect(r) // dereference from ptr
    return r.MethodByName("InsideReducer").
        Call([]reflect.Value{r})[0],
        v.FieldByName("State")
}
return r, r.Call(
    []reflect.Value{
    // We just use their zero value for initialize
        reflect.Zero(r.Type().In(0)), // In index 0 is state
        reflect.Zero(r.Type().In(1)), // In index 1 is action
    },
    )[0] // 0 at here is because checkReducer promise that we will only receive one return 
```

Enter fullscreen mode Exit fullscreen mode

同样，Kind 是 Ptr 的意思是它的`rematch.Reducer`。

记住`actualReducer, initState := getReducerAndInitState(r)`这一行，我们得到了`(reducer, state)`对。

现在，当我们收到由`InsideReducer`生产的`rematch.Reducer`、`reducer`时，它在哪里？我们没有在任何用户的代码中看到它，对吗？因为它是在包`rematch`中定义的，所以导出它是因为反射只能接受导出的成员！

其他都是原减速器(需要正常功能应用减速器)，我们不再赘述，你可以参考 [design-of-redux-go-v2](https://dannypsnl.github.io/golang/redux/2018/05/17/design-of-redux-go-v2.html) 了解更多信息。

回到 InsideReducer

```
// package rematch
func (r Reducer) InsideReducer(v interface{}) func(interface{}, *action) interface{} {
    r.ms = r.methods(v)
    return func(state interface{}, action *action) interface{} {
        return r.ms[action.reducerName()].Call(
            []reflect.Value{
                reflect.ValueOf(state),
                reflect.ValueOf(action.payload()),
            },
        )[0].Interface()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它最终返回一个正常的减速器，然后你会发现它非常依赖于`r.methods`。那是什么？我们来查看一下它的定义。

```
// package rematch
func (r Reducer) methods(v interface{}) map[string]reflect.Value {
    rv := reflect.ValueOf(v)
    rt := reflect.TypeOf(v)
    methods := make(map[string]reflect.Value)
    for i := 1; i < rt.NumMethod(); i++ {
        m := rt.Method(i) // rt.Method.Func return func with first argument as receiver
        mt := m.Type
        if mt.NumIn() == 3 &&
            mt.NumOut() == 1 &&
            mt.In(1) == mt.Out(0) {
            // rv.Method return func with now receiver
            methods[m.Name] = rv.Method(i)
        }
    }
    return methods
} 
```

Enter fullscreen mode Exit fullscreen mode

`methods`获取自定义重匹配器(回到`InsideReducer` & `getReducerAndInitState`，你会发现这个传递流)，概述每一个方法，如果任何东西看起来像一个内部缩减器，把它放入方法图。

现在你可能有几个困惑点。

1.  为什么使用`m.Name`，而不是地址
2.  为什么使用`mt.In(1)`，而不是`mt.In(0)`
3.  为什么`NumIn()`应该是 3

第一个问题的答案是，方法的实例和方法的类型有不同的地址！知道最终机器码里没有`user-type`就不难理解了。我们将创建一个表(或者其他东西，不重要)来表示`user-type`。但是我们可以获得相同的名称(type info 将存储它)。

第二个答案和第三个一样，反射类型的结构的方法`Method`返回方法的一个底层函数。

比如我们有一个类型`K`，`K`有一个方法`foo()`，这个世界上没有`K.foo()`，我们其实有`foo(*K)`，那就是`rt.Method(i)`给你的！

最后，我们来看看`action`。这个疯狂教程的最后一个谜题。

```
// package rematch
type action struct {
    funcName string
    with interface{}
} 
```

Enter fullscreen mode Exit fullscreen mode

这是它看起来的样子。我们将方法的名称和有效负载命名为`with`。

我们使用`Action`来创建我们的动作

```
// package rematch
func (r Reducer) Action(method interface{}) *action {
    return &action{
        funcName: getReducerName(method),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们先相信`getReducerName`工作正常，以后再提。

正如您所料，`With`只需设置有效载荷。

```
// package rematch
func (a *action) With(payload interface{}) *action {
    a.with = payload
    return a
} 
```

Enter fullscreen mode Exit fullscreen mode

`reducerName` & `payload`用在`InsideReducer`身上，不用解释，只要把动作保留下来的东西还回去就行了。

```
// package rematch
func (a action) reducerName() string {
    return a.funcName
}

func (a action) payload() interface{} {
    return a.with
} 
```

Enter fullscreen mode Exit fullscreen mode

是最模糊的东西，但是就像我们提到的，方法是一个函数，第一个参数是它的接收者！

```
// package rematch
func getReducerName(r interface{}) string {
    fullName := runtime.FuncForPC(reflect.ValueOf(r).Pointer()).Name()
    // fullName's format is `package.function_name`
    // we don't want package part.
    // package is full path(GOPATH/src/package_part) to it
    // len-3 is because a method contains suffix `-fm`
    return fullName[strings.LastIndexByte(fullName, '.')+1 : len(fullName)-3]
} 
```

Enter fullscreen mode Exit fullscreen mode

但是为什么是`len(fullName)-3`？原因是你可以同时拥有`Foo` & `Foo(*K)`！解决 Go pick 的所有方法都以`-fm`为后缀！

现在你知道我们为什么切它了。因为方法的类型。名称没有这个后缀，我们要映射它们，所以我们必须遵循它们的规则。

有了这些改变，现在我们可以处理 reducer 和 action 之间固有关系了！我猜还能睡个好觉？