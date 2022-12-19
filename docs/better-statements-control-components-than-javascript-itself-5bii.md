# 比 javascript 本身更好的条件操作符

> 原文：<https://dev.to/monsterooo/better-statements-control-components-than-javascript-itself-5bii>

在这篇[文章](https://blog.logrocket.com/conditional-rendering-in-react-c6b0e5af381e)中，我学到了一些东西，但也在思考一些东西。

### 条件运算符

内联条件运算符令人困惑，例如:

`condition ? expr_if_true : expr_if_false`

```
return (
  <div>
    <p>Text: {this.state.text}</p> 
    {
      view
      ? null
      : (
        <p>
          <input
            onChange={this.handleChange}
            value={this.state.inputText} />
        </p>
      )
    }

  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

或者根据变量值
显示组件

```
{
  view
  ? null
  : (
    <p>
      <input
        onChange={this.handleChange}
        value={this.state.inputText} />
    </p>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您有嵌套代码😰

```
return (
  <div>
    { condition1
      ? <Component1 />
      : ( condition2
        ? <Component2 />
        : ( condition3
          ? <Component3 />
          : <Component 4 />
        )
      )
    }
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

不知道能不能把这个语句抽象成一个 react 组件？

像这样

```
<If when={this.state.logic}>
  <p>↔️show component</p> </If> 
```

Enter fullscreen mode Exit fullscreen mode

```
<If when={this.state.logic}>
  <p>↔️show component</p>
  <If when={this.state.logic}>
    <p>other component</p>
  </If> </If> 
```

Enter fullscreen mode Exit fullscreen mode

我认为它更漂亮，可读性更强

还有很多

```
<Switch value={value}>
  <Case when={condition}>
    <p>condition 1</p>
  </Case>
  <Case when={condition}>
    <p>condition 2</p>
  </Case>
  <Case when='c' children={<p>condition 3</p>}/>
  <Default children={<p>default component</p>}/>
</Switch> 
```

Enter fullscreen mode Exit fullscreen mode

确定多个条件，并可以显示默认组件

迭代遍历`array`或`object`

```
<For of={['a', 'b', 'c']}>
  {(item, index) => (<p key={index}>{index}：{item}</p>)}
  <Default>default component</Default> </For> 
```

Enter fullscreen mode Exit fullscreen mode

我为此创建了一个回购协议

你有什么想法吗？

😮