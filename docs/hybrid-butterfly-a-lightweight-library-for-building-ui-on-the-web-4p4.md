# hybrid-butterfly 是一个轻量级的库，用于在 web 上构建 UI

> 原文：<https://dev.to/ahmedyoussef70/hybrid-butterfly-a-lightweight-library-for-building-ui-on-the-web-4p4>

# 杂交蝴蝶

一个轻量级的库，用于在 web 上构建 UI，具有独特而简单的语法，构建在 [butterfly-template](https://github.com/ahmedyoussef70/butterfly-template) 之上

# 开始

*   加拿大

```
<script src="https://cdn.jsdelivr.net/gh/ahmedyoussef70/hybrid-butterfly@0.0.4/dist/hybridButterfly.js"></script> <script>
  const { Component, render } = window.hybridButterfly;
</script> 
```

# 现场演示

[英雄之旅(翻拍原角例)](https://codesandbox.io/s/6yklz7xmzr)

# Github 回购

[杂交蝴蝶](https://github.com/ahmedyoussef70/hybrid-butterfly)

## hybrid-butterfly 到底给 [butterfly-template](https://github.com/ahmedyoussef70/butterfly-template) 添加了什么？

*   具有两个生命周期挂钩的组件/上下文`didMount` `willUnmount`

#### 注意:每个组件应该返回一个元素(比如 react.js)

```
class HelloWorld extends Component {
  didMount() {}

  willUnmount() {}

  template() {
    return `h1 [ "hello world" ]`
  }
} 
```

*   模板绑定

```
class HelloWorld extends Component {
  text = 'hello world'

  template() {
    return `h1 [ {{ text }} ]`
  }
} 
```

*   If/For Exp。

for loops 在其作用域中也有一些特殊的变量:`counter` `even` `odd`

*计数器*是从 0 开始的数字

*偶数*和*奇数*都是布尔值

```
class HelloWorld extends Component {
  x = true
  chars = ['a', 'b', 'c']

  template() {
    return `div [

      #if (x) [
        #for (char of chars) [
          h1 [ {{ char }} " - " {{ counter }} " - " {{ even }} " - " {{ odd }} ]
        ]
      ]

    ]`
  }
} 
```

*   组件解析

1 -依赖组件必须在组件属性中注册，然后在模板中作为标记使用

```
class XComponent extends Component {
  template() {
    return `h1 [ "hello from XComponent" ]`
  }
}

class HelloWorld extends Component {
  components = { XComponent }

  template() {
    return `div [
      XComponent
    ]`
  }
} 
```

2 -您也可以将数据/函数传递给其他组件

```
class XComponent extends Component {
  constructor(msg, moreData) {
    super()
    this.msg = msg
    this.moreData = moreData
  }

  template() {
    return `h1 [ {{ msg }} ]`
  }
}

class HelloWorld extends Component {
  components = { XComponent }

  msg = 'hello world'

  template() {
    return `div [
      XComponent ( "hello world", "hmmm" )
      XComponent ( {{ msg }}, 123 )
    ]`
  }
} 
```

*   事件处理程序

```
class HelloWorld extends Component {
  log1 = () => console.log('clicked')

  log2 = value => console.log(value)

  log3 = f => () => f('higher order functions')

  template() {
    return `div [
      button (@click="log1()") [ "click me" ]
      input (@keyup="log2(event.value)")
      button (@click={{ log3(log2) }}) [ "click me" ]
    ]
    `
  }
} 
```

## 如何更新 DOM？

只需在组件上调用`updateView`

```
class HelloWorld extends Component {
  msg = 'hello'

  didMount() {
    setTimeout(() => {
      this.msg = 'bye'
      this.updateView()
    }, 1000)
  }

  template() {
    return `h1 [ "${this.msg}" ]`
  }
} 
```

## 如何将你的应用渲染到 DOM？

使用您的应用程序条目和您想要添加应用程序的 DOM 元素调用`render`

```
class HelloWorld extends Component {
  msg = 'hello world'

  template() {
    return `h1 [ "${this.msg}" ]`
  }
}

render(HelloWorld, document.querySelector('#app')) 
```