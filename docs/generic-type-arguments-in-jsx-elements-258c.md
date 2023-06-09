# JSX 元素中的泛型类型参数

> 原文：<https://dev.to/ganderzz/generic-type-arguments-in-jsx-elements-258c>

原贴:[此处](https://www.dylanpaulus.com/react/typescript/2018/06/02/generic-jsx-type-argument/)

# JSX 元素中的类属式实参

Typescript 最近在其 2.9 版本中发布了 JSX 的泛型类型参数。它是满满的，但这对我们意味着什么呢？让我兴奋的一个常见用例是允许库的消费者扩展组件的道具。使用[动态组件](https://www.dylanpaulus.com/react/2017/07/26/injecting-react-tag-types/)我们将着眼于允许我们的组件扩展更多。

### 什么是泛型类型参数？

如 [Typescript 发行说明](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#generic-type-arguments-in-jsx-elements)所示，泛型类型参数是使用 Typescript 的泛型语法创建组件的一种方式。下面是旧方法与使用泛型类型参数的并排比较。

#### **老办法:**

```
// Notice color isn't defined as a prop, and will error out normally
function Div(props: { value: string }) {
    const { value, ...rest } = this.props;

    return <div {...rest} />
}

// Using spread, we can trick Typescript into ignoring that color will be a prop
// on Div
function App() {
    return <Div {...{ color: "red" }} />
} 
```

Enter fullscreen mode Exit fullscreen mode

#### **类属式论据:**

```
// Notice our new generic on the component
function Div<T extends object>(props: { value: string } & T) {
    const { value, ...rest } = props as any; // spreading on generics not yet supported

    return <div {...rest} />
}

interface IAdditionalProps {
    color: string;
}

// We can tell typescript our Div element has additional properties!
function App() {
    // Generic Type Arguments!
    return <Div<IAdditionalProps> color="red" value="TEXT!!!" />
} 
```

Enter fullscreen mode Exit fullscreen mode

并且同样可以用于类组件:

```
// Notice our new generic on the component
class Div<T extends object> extends React.Component<{ value: string } & T> {
    public render() {
        const { value, ...rest } = this.props as any;

        return <div {...rest} />
    }
}

interface IAdditionalProps {
    color: string;
}

// We can tell typescript our Div element has additional properties!
function App() {
    return <Div<IAdditionalProps> color="red" value="TEXT!!" />
} 
```

Enter fullscreen mode Exit fullscreen mode

### 动态元素

假设我们有一个 **MenuItem** 组件，它可能被一个路由器链接组件或者一个 html *标签重载。我们可以这样写...* 

```
interface IProps {
    tag: React.ReactNode;
    children: React.ReactNode;
}

function MenuItem({ tag, children, ...rest }: IProps) {
    const Tag: React.ReactType = tag || "a";

    return (
        <Tag {...rest}>
            {children}
        </Tag>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

MenuItem 作为一个组件工作得非常好，但是当需要添加额外的属性时，Typescript 会大喊。例如， *a* 标签需要一个 *href* 道具。我们不想硬编码 *href* ，因为我们可以通过*标签*道具(React 路由器、按钮等)注入任何类型的元素。

```
<MenuItem tag="a" href="http://google.com">Click Me!</MenuItem> // Error because href isn't defined in IProps!
<MenuItem tag={Link} to="/home">Home</MenuItem> // Error because to isn't defined in IProps! 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用泛型类型参数来修复错误。

```
interface IProps {
  tag: React.ReactNode;
  children: React.ReactNode;
}

function MenuItem<T extends object>(props: IProps & T) {
  const { tag, children, ...rest } = props as any;
  const Tag: React.ReactType = tag || "a";

  return (
      <Tag {...rest}>
          {children}
      </Tag>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的 **MenuItem** 组件的消费者可以告诉我们还需要什么附加属性！

```
<MenuItem<{ href: string }> tag="a" href="http://google.com">Click Me!</MenuItem> // Success!
<MenuItem<{ to: string }> tag={Link} to="/home">Home</MenuItem> // Success! 
```

Enter fullscreen mode Exit fullscreen mode

通过 JSX 的泛型类型参数，我们能够使我们的组件更加可重用。用户可以扩展组件以允许额外的道具。太好了！