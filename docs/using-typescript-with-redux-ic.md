# 通过 Redux 使用 TypeScript

> 原文：<https://dev.to/brightdevs/using-typescript-with-redux-ic>

最近我们已经检查了【TypeScript 在我们的 React 项目中增加了什么好处。现在，让我们沿着前端项目如今所走的典型道路，将 [Redux](https://brightinventions.pl/blog/from-react-to-react-redux-in-a-few-steps/) 添加到组合中。不幸的是，默认情况下，Redux 并不是真正的类型安全，因为在大多数情况下，它表现为[切换带有任意有效负载的动作](https://redux.js.org/introduction/core-concepts)。但是 TypeScript 是一只强大的野兽，只要稍加小心，我们就可以使 Redux 代码不仅是类型安全的，而且更干净、可读性更好。

[![Redux + TypeScript](../Images/f41ee3d750e6156997f081c08fc0e937.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nbopMMEt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5jg2toq7qgzvh0mcm3re.png)

## 基本打字

让我们从“[核心概念](https://redux.js.org/introduction/core-concepts)”介绍性 Redux guide 的页面一步步剖析经典的待办事项列表示例。全球应用程序的状态最初如下:

```
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
} 
```

Enter fullscreen mode Exit fullscreen mode

为这个状态创建一个类型定义非常简单:

```
interface Todo {
    text: string
    completed: boolean
}

type VisibilityFilter = 'SHOW_COMPLETED' | 'SHOW_ALL'

interface AppState {
    todos?: Todo[]
    visibilityFilter?: VisibilityFilter
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`AppState`的属性是可选的，因为状态最初可能是空的，我们需要在代码中处理它，从而在类型定义中反映它。

到目前为止一切顺利。现在行动——它们最初看起来是这样的:

```
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用由 [`redux`库本身](https://github.com/reactjs/redux/blob/master/index.d.ts)提供的类型，特别是`AnyAction`，它只强制设置`type`属性:

```
const actions: AnyAction[] = [
    { type: 'ADD_TODO', text: 'Go to swimming pool' },
    { type: 'TOGGLE_TODO', index: 1 },
    { type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
] 
```

Enter fullscreen mode Exit fullscreen mode

总比没有好，我们不能有一个没有类型的动作，至少我们可以把任意数据放入其中。

现在让我跳到减速器。原始示例中的减速器是这样实现的:

```
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}
​
function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map(
        (todo, index) =>
          action.index === index
            ? { text: todo.text, completed: !todo.completed }
            : todo
      )
    default:
      return state
  }
}

// top-level reducer
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们首先使用我们的状态和动作类型定义:

```
function visibilityFilter(state: VisibilityFilter = 'SHOW_ALL', action: AnyAction): VisibilityFilter {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}
​
function todos(state: Todo[] = [], action: AnyAction): Todo[] {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map(
        (todo, index) =>
          action.index === index
            ? { text: todo.text, completed: !todo.completed }
            : todo
      )
    default:
      return state
  }
}

// top-level reducer
function todoApp(state: AppState = {}, action: AnyAction): AppState {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，所有的归约器，无论它们存在于哪个级别，都共享相同的通用签名:

```
type Reducer<S> = (state: S, action: AnyAction) => S; 
```

Enter fullscreen mode Exit fullscreen mode

这正是内置的 Redux 类型提供的类型，所以如果我们出于某种原因迫切需要对它进行更多的推广，我们可以直接在代码中使用它。

## 我们能做的更多吗？

到目前为止，我们所做的只是添加了基本的类型定义，可以防止我们出现一系列的拼写错误。但是看一下 reducers，我们离真正的类型安全还很远，因为`AnyAction`类型化的动作——顾名思义就是这个`any`——并没有给 TypeScript 编译器任何线索，说明特定的动作类型实际上携带了什么样的有效载荷。在访问动作的有效负载属性时，我们仍然可能会打错字，假定它的类型是错误的，或者错误地使用了另一种类型的动作的属性，在这种情况下，TypeScript 没有工具来警告我们:

```
function visibilityFilter(state: VisibilityFilter = 'SHOW_ALL', action: AnyAction): VisibilityFilter {
    if (action.type === 'SET_VISIBILITY_FILTER') {
        return action.fliter // note the typo here. TypeScript is unable to detect the problem here
    } else {
        return state
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是所有的希望还没有破灭。TypeScript 有一个强大的功能，即[受歧视的工会](https://basarat.gitbooks.io/typescript/docs/types/discriminated-unions.html),尽管这个名字听起来像是某种社会少数群体的组织，但它可能在这里对我们有很大帮助。

让我们为我们支持的每种动作创建详细的类型定义:

```
interface AddTodoAction extends Action {
    type: 'ADD_TODO'
    text: string
}

interface ToggleTodoAction extends Action {
    type: 'TOGGLE_TODO'
    index: number
}

interface SetVisibilityFilterAction extends Action {
    type: 'SET_VISIBILITY_FILTER'
    filter: VisibilityFilter
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们现在扩展了 Redux 提供的`Action`，它只指定了一个`type`属性。`AnyAction`我们之前使用的类型允许任何属性存在于它的实现中，所以它破坏了我们想要的类型安全。这里另一个有趣的地方是，我们用文字指定了`type`属性，使它成为我们被鉴别的联合的鉴别器的一个很好的候选。这里是联盟本身:

```
type TodoAppAction = AddTodoAction | ToggleTodoAction | SetVisibilityFilterAction 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们用`TodoAppAction`代替减速器中的`AnyAction`，看看现在会发生什么:

```
function visibilityFilter(state: VisibilityFilter = 'SHOW_ALL', action: TodoAppAction): VisibilityFilter {
    if (action.type === 'SET_VISIBILITY_FILTER') {
        return action.filter
    } else {
        return state
    }
}
​
function todos(state: Todo[] = [], action: TodoAppAction): Todo[] {
    switch (action.type) {
        case 'ADD_TODO':
            return state.concat([{text: action.text, completed: false}])
        case 'TOGGLE_TODO':
            return state.map(
                (todo, index) =>
                    action.index === index
                        ? {text: todo.text, completed: !todo.completed}
                        : todo
            )
        default:
            return state
    }
}

function todoApp(state: AppState = {}, action: TodoAppAction): AppState {
    return {
        todos: todos(state.todos, action),
        visibilityFilter: visibilityFilter(state.visibilityFilter, action)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用一个支持类型脚本的 IDE(例如像 [WebStorm](https://www.jetbrains.com/webstorm/) )，您可能会注意到动作属性现在被正确地着色了，代码自动完成的工作方式与我们想象的完全一样。现在让我们再次尝试我们的打字错误示例:

[![TypeScript properly checks Action payload types](../Images/db8ed5d5e8a48428beb8a6c2aceb59df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7CHa9-9P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ic0gx2s3wbxxcuipfdob.png)

人类的胜利，不是吗？

## 连接组件

另一个我们可能感觉被 Redux 背叛的地方是它实际上[绑定到 React 组件](https://redux.js.org/basics/usage-with-react)的地方。让我们再次使用 Redux guide 中的一个简单的例子:

```
import { connect } from 'react-redux'
import { setVisibilityFilter } from '../actions'

// the component itself

const Link = ({active, children, onClick}) => {
  if (active) {
    return <span>{children}</span>
  }
​
  return <a href="" onClick={onClick}>
    {children}
  </a>
}
​
// redux binding

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}
​
const mapDispatchToProps = (dispatch, ownProps) => {
  return {
    onClick: () => {
      dispatch(setVisibilityFilter(ownProps.filter))
    }
  }
}
​
const FilterLink = connect(mapStateToProps, mapDispatchToProps)(Link) 
```

Enter fullscreen mode Exit fullscreen mode

让我们以简单的方式定义`Link`组件的属性类型:

```
interface LinkProps {
    active: boolean
    onClick: () => void
}

const Link: React.StatelessComponent<LinkProps> = props => {
  if (props.active) {
    return <span>{props.children}</span>
  }
​
  return <a href="" onClick={props.onClick}>
      {props.children}
  </a> } 
```

Enter fullscreen mode Exit fullscreen mode

这样做很好，但是这样我们就不能再深入到`mapStateToProps`或`mapDispatchToProps`中，因为这些方法是由独立的部分组成我们最终的道具对象的，我们要么让它不类型化，要么用某种类似`Partial<LinkProps>`的变通方法来描述它，至少给我们一些基本的检查:

[![Workaround on mapStateToProps might help a bit](../Images/ea6fa688cd30a90146ac04c729bd587c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H6SI4_lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ici50whbv60va9952073.png)

注意这里我们也没有合适的类型用于`ownProps`，为了方便起见，我们使用了`any`。此外，这种方法让我们返回这里的`onClick`财产，在这种情况下，我们显然希望只从`mapDispatchToProps`返回。让我们明确一下，把我们的道具分成三个独立的类型:一个是自己的道具(从父组件传递过来)，一个是基于状态的道具，一个是基于派遣的道具:

```
interface LinkOwnProps {
    filter: VisibilityFilter
}

interface LinkStateProps {
    active: boolean
}

interface LinkDispatchProps {
    onClick: () => void
}

type LinkProps = LinkOwnProps & LinkStateProps & LinkDispatchProps 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的 Redux 绑定是完全类型安全的:

```
const mapStateToProps = (state: AppState, ownProps: LinkOwnProps): LinkStateProps => {
    return {
        active: ownProps.filter === state.visibilityFilter
    }
}

const mapDispatchToProps = (dispatch: Dispatch<AppState>, ownProps: LinkOwnProps): LinkDispatchProps => {
    return {
        onClick: () => {
            dispatch(setVisibilityFilter(ownProps.filter))
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，正确的 TypeScript 定义所提供的类型安全使 Redux 再次变得伟大。你呢？

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)