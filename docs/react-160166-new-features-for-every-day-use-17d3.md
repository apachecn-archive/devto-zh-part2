# React 16.0—16.6 日常使用的新功能

> 原文：<https://dev.to/sapegin/react-160166-new-features-for-every-day-use-17d3>

这是一个简短的备忘单，适用于从 React 15 迁移到 React 16，或者从更早的 16.x 版本迁移到 16.6 的开发人员。它侧重于您经常使用的功能。

## 从带有片段的组件中返回多个元素

将 UI 分割成小的可重用组件可能会导致创建不必要的 DOM 元素，比如当您需要从一个组件返回多个元素时。React 16 有几个选项可以避免这种情况:

```
// React 15: extra wrapper element
const Breakfast = () => (
  <ul>
    <li>Coffee</li>
    <li>Croissant</li>
    <li>Marmalade</li>
  </ul> );

// React 16.0: array (note that keys are required)
const Breakfast = () => [
  <li key="coffee">Coffee</li>,
  <li key="croissant">Croissant</li>,
  <li key="marmalade">Marmalade</li> ];

// React 16.2: fragment
const Breakfast = () => (
  <React.Fragment>
    <li>Coffee</li>
    <li>Croissant</li>
    <li>Marmalade</li>
  </React.Fragment> );

// React 16.2: fragment (short syntax)
const Breakfast = () => (
  <>
    <li>Coffee</li>
    <li>Croissant</li>
    <li>Marmalade</li>
  </> );

// React 16: fragments composition
const Meals = (
  <ul>
    <Breakfast />
    <Lunch />
    <Dinner />
  </ul> ); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您正在使用的工具可能不支持短语法。

## 从组件返回字符串和数字

在 React 16 中，组件可以返回字符串和数字。这对于不需要任何标记的组件很有用，比如国际化或格式化:

```
// React 15
const LocalDate = ({ date }) => (
  <span>
    {date.toLocaleDateString('de-DE', {
      year: 'numeric',
      month: 'long',
      day: 'numeric'
    })}
  </span> );

// React 16
const LocalDate = ({ date }) =>
  date.toLocaleDateString('de-DE', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 取消 setState()以避免重新渲染

在 React 15 中，如果你的下一个状态是基于上一个状态，那么取消`setState()`并避免重新渲染是不可能的。在 React 16 中，你可以在`setState()`的回调:
中返回`null`

```
// React 16
handleChange = event => {
  const city = event.target.value;
  this.setState(
    prevState => (prevState.city !== city ? { city } : null)
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，使用与州名相同的城市名调用`handleChange()`不会导致重新呈现。

## 用官方上下文 API (16.3)避免道具钻取

[prop drilling](https://blog.kentcdodds.com/prop-drilling-bb62e02cb691) 是当你使用一个 Prop 将一些数据传递给一个深度嵌套的组件时，所以你必须将这个 Prop 添加到 React 组件树的每一层，在拥有数据的组件和消费数据的组件之间。

```
class Root extends React.Component {
  state = { theme: THEME_DARK };
  handleThemeToggle = theme =>
    this.setState(({ theme }) => ({
      theme: theme === THEME_DARK ? THEME_LIGHT : THEME_DARK;
    }));
  render() {
    return (
      <Page
        onThemeToggle={this.handleThemeToggle}
        {...this.state}
        {...this.props}
      />
    );
  }
}

// Each layer will have to pass theme and theme toggle handler props
<SomeOtherComponent
  onThemeToggle={props.onThemeToggle}
  theme={props.theme}
/>; 
// Many layers below
const Header = ({ theme, onThemeToggle }) => (
  <header className={cx('header', `header--${theme}`)}>
    ...
    <button onClick={onThemeToggle}>Toggle theme</button>
  </header> ); 
```

Enter fullscreen mode Exit fullscreen mode

这是一大堆样板代码！有了[上下文 API](https://reactjs.org/docs/context.html) ，我们可以在组件树的任何地方访问我们的主题道具:

```
const ThemeContext = React.createContext(THEME_DARK);

// We should wrap our app in this component
class ThemeProvider extends React.Component {
  state = { theme: THEME_DARK };
  handleThemeToggle = theme =>
    this.setState(({ theme }) => ({
      theme: theme === THEME_DARK ? THEME_LIGHT : THEME_DARK
    }));
  render() {
    return (
      <ThemeContext.Provider
        value={{
          onThemeToggle: this.handleThemeToggle,
          theme: this.state.theme
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}

// And then use theme consumer anywhere in the component tree
const Header = () => (
  <ThemeContext.Consumer>
    {({ theme, onThemeToggle }) => (
      <header className={cx('header', `header--${theme}`)}>
        ...
        <button onClick={onThemeToggle}>Toggle theme</button>
      </header>
    )}
  </ThemeContext.Consumer> ); 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 上查看一个例子](https://codesandbox.io/s/l43j6j2n7z)。

## 使用 getDerivedStateFromProps()更新基于 props 的状态(16.3)

`getDerivedStateFromProps()`生命周期方法是对`componentWillReceiveProps()`的替代。当您有一个带有状态属性默认值的属性，但您想在该属性改变时重置状态时，这很有用。例如，一个模态有一个属性表示它最初是否打开，还有一个状态表示一个模态现在是否打开:

```
// React 15
class Modal extends React.Component {
  state = {
    isOpen: this.props.isOpen
  };
  componentWillReceiveProps(nextProps) {
    if (nextProps.isOpen !== this.state.isOpen) {
      this.setState({
        isOpen: nextProps.isOpen
      });
    }
  }
}

// React 16.3
class Modal extends React.Component {
  state = {};
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.isOpen !== prevState.isOpen) {
      return {
        isOpen: nextProps.isOpen
      };
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当一个组件被创建并且当它接收到新的属性时，`getDerivedStateFromProps()`方法被调用，所以你不必两次将属性转换为状态(在初始化时和在`componentWillReceiveProps`中)。

## 用 React.memo()改变道具上的重新渲染功能组件(16.6)

[React.memo()](https://reactjs.org/docs/react-api.html#reactmemo) 对函数组件的操作与 [PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 对类组件的操作相同:只有当属性改变时才重新呈现组件。

```
const MyComponent = React.memo(props => {
  /* Only rerenders if props change */
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 contextType 更容易访问类组件中的上下文(16.6)

[Class.contextType](https://reactjs.org/blog/2018/10/23/react-v-16-6.html#static-contexttype) 简化了对类组件 :
中的 React 上下文*的访问*

```
class App extends React.Component {
  static contextType = ThemeContext;
  componentDidMount() {
    const { theme } = this.context;
    /* ... */
  }
  componentDidUpdate() {
    const { theme } = this.context;
    /* ... */
  }
  componentWillUnmount() {
    const { theme } = this.context;
    /* ... */
  }
  render() {
    const { theme } = this.context;
    return (
      <h1>
        {theme === THEME_DARK
          ? 'Welcome to the dark side!'
          : 'Welcome to the light side!'}
      </h1>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他新特性

React 16 还有许多在某些情况下有用的其他功能:

*   [误差边界](https://reactjs.org/docs/error-boundaries.html)；
*   [门户](https://reactjs.org/docs/portals.html)；
*   [转发参考](https://reactjs.org/docs/forwarding-refs.html)(16.3)；
*   [getSnapshotBeforeUpdate()生命周期方法](https://reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)(16.3)；
*   [严格模式组件](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)(16.3)；
*   [指针事件](https://reactjs.org/blog/2018/05/23/react-v-16-4.html#pointer-events)(16.4)；
*   [剖面图【t1(16.5)】；](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)
*   [react . lazy](https://reactjs.org/blog/2018/10/23/react-v-16-6.html#reactlazy-code-splitting-with-suspense)(16.6)；
*   [static getDerivedStateFromError()](https://reactjs.org/blog/2018/10/23/react-v-16-6.html#static-getderivedstatefromerror) (16.6).

我也强烈推荐尼克·格拉芙在 Egghead 开设的 React 16 课程。