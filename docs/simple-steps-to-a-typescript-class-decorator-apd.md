# Typescript 类装饰器的简单步骤

> 原文：<https://dev.to/phillipmalboeuf/simple-steps-to-a-typescript-class-decorator-apd>

我一直在努力实现 React 组件类的类装饰器。我需要它能够在我的组件的生命周期方法中使用上下文。如果你不确定 React 16.3 上下文 API 是如何工作的，我已经整理了一份我认为最容易理解的文章列表。如果您熟悉 API，请继续阅读，探索 Typescript 类装饰器的不同部分。

#### React 上下文 API 上的文章列表

*   一篇平易近人的文章，有清晰的用例(还有零食！):[![sreisner image](img/7ae9b650073b2e86ebc16a9c516c2137.png)](/sreisner) [## 学习反应上下文 API，并将其应用到您的应用程序中

    ### Shawn Reisner Jun 14 ' 184min read

    # react# web dev# material design](/sreisner/understanding-the-react-context-api-through-building-a-shared-snackbar-for-in-app-notifications-8j2)
*   稍微深入一点:[介绍 Leigh Halliday 的 React 上下文 API](https://www.leighhalliday.com/introducing-react-context-api)
*   当然，还有写得很好的:[官方 React 文档上的上下文](https://reactjs.org/docs/context.html)

## 现在，一个类型脚本类装饰器

类装饰器用于修改类的构造函数。换句话说，您可以方便地打乱类实例的实例化阶段。因此，Typescript 中的类装饰器(什么也不做)可能有这样的结构:

```
export function decorator<C>(ClassDefinition: C): C {
  return ClassDefinition
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`C`是类定义的泛型类型。如果您以前没有遇到过泛型文档，请翻阅一下:【React 官方文档中的泛型

有了这个实现，你可以在需要用`@` :
修改的类上使用你的装饰器

```
import { decorator } from './decorator'

@decorator
export class Cat {} 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来，React 组件类装饰器

所以我们的目标是修改一个 React 组件类，让它消费一个上下文，对吧。首先，让我们重命名一些东西，并添加`React.ComponentClass`类型定义。

```
import * as React from 'react'

export function withContext<C extends React.ComponentClass>(Component: C): C {
  return Component
} 
```

Enter fullscreen mode Exit fullscreen mode

现在 Typescript 将`C`识别为 ComponentClass。你:但是伙计，我们的消费者呢？我:我们快到了！让我们先实例化我们的上下文。

## 初始化一个上下文

接下来，我们将使用一个“宠物店”作为我们的示例用例，它有一个“购物车”和将猫添加到购物车的功能。因此，我们的上下文将有一个名为`cart`的猫数组和一个名为`addPetToCart`的函数。在 Typescript 中创建上下文要求您提供默认值，包括它们的类型定义。下面是我们的上下文初始化:

```
import * as React from 'react'

export const StoreContext = React.createContext({
  cart: [] as {catId: number}[],
  addPetToCart: (catId: number)=> function(): void {}
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 编写我们的上下文装饰器

好了，我们终于写了一些反应。并且 [React 文档告诉我们](https://reactjs.org/docs/context.html#accessing-context-in-lifecycle-methods)使用上下文的最佳方式是实现一个 HOC 并将其传递给 props 中的子组件。所以在我们的`StoreContext`初始化之后，我们可以写一个`StoreContext.Consumer`无状态组件作为我们的装饰器，比如:

```
import * as React from 'react'

export const StoreContext = React.createContext({
  cart: [] as {catId: number}[],
  addPetToCart: (catId: number)=> function(): void {}
})

export function withStoreContext<C extends React.ComponentClass>(Component: C): C {
  return (props => <StoreContext.Consumer>
      {context => <Component {...props} context={context} />}
    </StoreContext.Consumer>) as any as C
} 
```

Enter fullscreen mode Exit fullscreen mode

读到这里，你可能会对我大喊:这是什么垃圾！？问题是，是的，类装饰器应该返回一个类定义，而 Typescript 不允许我们尝试返回一个函数。但是另一方面，React 可以互换地接受“T1”和“T2”。因此，我很满意将这个`SFC`函数强制转换成我们的`C`类定义。

## 编写我们的提供者组件类

现在让我们把装饰器放在一边，让我们继续写我们的`StoreContext.Provider`组件。这里是我们可以实现宠物店功能的地方:将`cart`置于组件的状态，并定义一个`addPetToCart`函数。照这样:

```
import * as React from 'react'
import { BrowserRouter, Switch, Route } from 'react-router-dom'

import { StoreContext } from './context'
import { Pets } from './pets'

interface StoreProps {}
interface StoreState {
  cart: {catId: number}[]
}

export class Store extends React.Component<StoreProps, StoreState> {
  constructor(props: StoreProps) {
    super(props)
    this.state = {
      cart: []
    }
  }

  public addPetToCart(catId: number): void {
    this.setState({ cart: [...this.state.cart, { catId }] })
  }

  public render() {
    return <StoreContext.Provider value={{
        cart: this.state.cart,
        addPetToCart: this.addPetToCart.bind(this)
      }}>
      <BrowserRouter>
        <Switch>
          <Route path='/' component={Homepage} />
        </Switch>
      </BrowserRouter>
    </StoreContext.Provider>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到一个`BrowserRouter`的存在。我只是发现在`Route`的深处拥有组件是上下文 API 应用程序的一个常见用例。女士们先生们，我们有进展了！

## 编写我们的消费者组件类

对于我们的最后一圈，我们将有一个在我们的`cats`集合中渲染的`Homepage`组件和一个`Cart`组件。因为`Cat`组件需要从商店上下文中访问`addPetToCart`函数，而`Cart`也将从商店上下文中读取`cart`数组，所以我们给了它们两个`withStoreContext`装饰符:

```
import * as React from 'react'
import { RouteComponentProps } from 'react-router-dom'

import { withStoreContext } from './context'

interface HomepageProps extends RouteComponentProps<any> {}
interface HomepageState {
  cats: CatProps[]
}

export class Pets extends React.Component<HomepageProps, HomepageState> {
  constructor(props: HomepageProps) {
    super(props)
    this.state = {
      cats: [{id: 1, name: 'Garfield'}, {id: 2, name: 'Mufasa'}]
    }
  }

  public render() {
    return <>
      {this.state.cats.map(cat => <Cat key={cat.id} {...cat} />)}
      <Cart />
    </>
  }
}

interface StoreContextProps {
  context?: {
    cart: {catId: number}[],
    addPetToCart: (catId: number)=> void
  }
}

interface CatProps extends StoreContextProps {
  id: number,
  name: string
}
interface CatState {}

@withStoreContext
export class Cat extends React.Component<CatProps, CatState> {
  constructor(props: CatProps) {
    super(props)
    this.state = {}
  }

  public render() {
    return <div>
      <strong>{this.props.name}</strong>
      <button onClick={()=> this.props.context.addPetToCart(this.props.id)}>Add to Cart</button>
    </div>
  }
}

interface CartProps extends StoreContextProps {}
interface CartState {}

@withStoreContext
export class Cart extends React.Component<CartProps, CartState> {
  constructor(props: CartProps) {
    super(props)
    this.state = {}
  }

  public render() {
    return <ol>
      {this.props.context.cart.map((item, index)=> <li key={index}>{item.catId}</li>)}
    </ol>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您会发现为我们的消费者组件定义一个`StoreContextProps`接口很有用。下一步将是我们的最后一步！

## 让我们渲染成 DOM

剩下的就是将`ReactDOM.render`转换成一个文档元素:

```
import * as React from 'react'
import * as ReactDOM from 'react-dom'

import { Store } from './store'

ReactDOM.render(<Store />, document.getElementById('store')) 
```

Enter fullscreen mode Exit fullscreen mode

## 提问&评论

感谢您阅读本文，如果您有任何问题或评论，请不要犹豫！这是在[地块](https://parceljs.org/)上运行的完整回购:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ phillipmalboeuf ](https://github.com/phillipmalboeuf) / [简单步骤到打字稿类装饰](https://github.com/phillipmalboeuf/simple-steps-to-a-typescript-class-decorator)

<article class="markdown-body entry-content container-lg" itemprop="text">

# Typescript 类装饰器的简单步骤

我一直在努力实现 React 组件类的类装饰器。我需要它能够在组件的生命周期方法中使用上下文。如果你不确定 React 16.3 上下文 API 是如何工作的，我已经整理了一份我认为最容易理解的文章列表。如果您熟悉 API，请继续阅读，探索 Typescript 类装饰器的不同部分。

阅读关于[开发到](https://dev.to/phillipmalboeuf/simple-steps-to-a-typescript-class-decorator-apd)的完整文章！

</article>

[View on GitHub](https://github.com/phillipmalboeuf/simple-steps-to-a-typescript-class-decorator)