# 重组で参考を扱う

> 原文：<https://dev.to/jgs/recompose--ref---2jmd>

前几天写了一篇报道([正在尝试 Storybook Driven Development 的](https://dev.to/jgs/storybook-driven-development---37b7))，因为使用 recompose 的话可以将外观上的 component 和逻辑分离开来写，所以很清爽。

制作 APP 表时，往往会发生用`componentDidUpdate`等参照`ref`的情况。 一般用`class`写的话就是

```
class extends React.Component {
  componentDidUpdate() {
    this.textarea // => <textarea />
  }

  render() {
    return (
      <textarea ref={textarea => (this.textarea = textarea)} />
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样写，使用 recompose 的话会出现

```
const enhance = lifecycle({
  componentDidUpdate() {
    // ???
  }
})

enhance(() => (<textarea ref={???} />)) 
```

Enter fullscreen mode Exit fullscreen mode

这样的形式分离。 此时，如上所示，即使用`ref`代入`this`，在`componentDidUpdate`之中`this`成为`ProxyComponent`，无法读出`ref`。 为了解决这个，并用`withHandlers`。

```
const enhance = compose(
  withHandlers(() => {
    let refs = {}
    return {
      onRef: props => textarea => (refs.textarea = textarea),
      onUpdate: props => refs.textarea // として参照できる!!
    }
  }),
  lifecycle({
    componentDidUpdate() {
      this.props.onUpdate()
    }
  })
)

enhance(props => (<textarea ref={props.onRef} />)) 
```

Enter fullscreen mode Exit fullscreen mode

这样，recompose 也可以处理 ref 了。