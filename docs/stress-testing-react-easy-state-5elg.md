# 压力测试反应轻松状态

> 原文：<https://dev.to/solkimicreb/stress-testing-react-easy-state-5elg>

第一部分是我通常介绍的 React Easy 状态。如果您已经熟悉，请跳到下一部分。

[React Easy State](https://github.com/solkimicreb/react-easy-state) 是一个透明的反应式状态管理库，有两个函数和两个伴随规则。

1.  始终用`view()`包装您的组件。
2.  总是用`store()`包装你的状态存储对象。

```
import React from 'react'
import { store, view } from 'react-easy-state'

const counter = store({
  num: 0,
  incr: () => counter.num++
})

export default view(() =>
  <button onClick={counter.incr}>{counter.num}</button>
) 
```

这足以让它在需要时自动更新您的视图。无论您如何构建或改变您的状态存储，任何语法上有效的代码都可以工作。

## 支持索赔

> 无论您如何构建或改变您的状态存储，任何语法上有效的代码都可以工作。

介绍的最后一句不是一个修辞语句，我想用一个简单的应用程序来证明这一点。

```
import React from 'react'
import { view, store } from 'react-easy-state'

const users = window.users = store([])

function UsersApp () {
  return users.map((user, idx) =>
    <div key={idx}>{user.name} <small>{user.email}</small></div>
  )
}

export default view(UsersApp) 
```

这个代码片段创建了一个`users`数组，并在`window.users`上对其进行全局公开，然后`UsersApp`将该数组映射到一个简单的 React 视图。

我们将从开发者控制台以越来越奇特的方式变异`users`数组，并观察`UsersApp`如何反应。提示:当用户以任何方式被添加、删除或修改时，它应该自动重新呈现。

## #1。预热:添加新用户

让我们首先向`users`数组添加一个简单的用户，然后更改他们的电子邮件地址。

```
users.push({ name: 'Bob', email: 'bob@gmail.com' })
users[0].email = 'bOb1.gmail.com' 
```

简单状态知道`UsersApp`在其渲染期间迭代`users`数组，并且知道`user.email`是为每个用户渲染的。由此推断出`UsersApp`应该在上述状态变化后重新渲染。

[![Adding a new user](img/35654003133da2e84c4570db445d827b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z66jqZ66--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qo3k3l2ruwojrc90xjd.gif)

#### [试试现场试玩吧！](https://easy-state-stress-test.now.sh/)

您可以跟随实时示例应用程序。不要惊讶，这是一个几乎空白的页面。您可以从开发人员控制台用用户填充它——就像我一样。

## #2。动态性能

第一个例子并不令人印象深刻。通过将这些行一个接一个地粘贴到控制台，让我们看看 ES6 独有的东西。

```
users[1] = { name: 'Ann' }
users[1].email = 'ann@windowslive.com'
delete users[1].email 
```

[![Dynamic properties](img/2854566fe2206f77c0826e0f533c4f37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HrOTDNqN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gl81qi2f235orwjao6f4.gif)

这一次，安是由`users[1] = newUser`而不是`push`添加的，最初，她没有电子邮件。动态添加新属性(如电子邮件)只能由 ES6 代理跟踪。在这些情况下，旧的透明状态管理库不会重新呈现应用程序。

`delete`是另一项直到最近才得以追踪的行动。如果你`delete`了一个在渲染过程中被使用的属性，那么简单状态会重新渲染这个`UsersApp`。

## #3。属性访问器和枚举

是时候进行更深入的探索了。对于这个例子，我们需要一些准备代码。

```
const Dave = {
  name: 'Dave',
  emails: { primary: 'dave@gmail.com' },
  get email () {
    const result = []
    for (const type in this.emails) {
      result.push(`${type}: ${this.emails[type]}`)
    }
    return result.join(', ')
  }
} 
```

Dave 是一个拥有多个电子邮件地址的用户。属性 getter 用于通过枚举`emails`对象并从中构造一个字符串来动态生成他的电子邮件列表。

```
users.push(Dave)
users[2].emails.secondary = 'dave@risingstack.com'
delete users[2].emails.primary 
```

[![Property accesors](img/f95d9971d2a292d99e181cd2d006d559.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eSANQWLv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1e78sxizfnz4stzfvu5.gif)

Easy State 知道 Dave 的电子邮件是从他的`emails`集合中的所有属性生成的。如果一封新邮件被添加到集合中，它会重新渲染`UsersApp`来反映它。当电子邮件被删除或修改时，也会发生同样的情况。

## #4。最后的老板:继承

让我们看看如果我们把原型继承加入其中会发生什么。这个例子还需要一个准备脚本。

```
const John = {
  email: 'john@gmail.com',
  name: 'John'
}

Object.setPrototypeOf(John, users[1]) 
```

新用户 John 从第二个用户 Ann 那里继承了他的名字和电子邮件。

```
users.push(John)
delete users[3].name
users[1].name = 'Ben'
users[3].name = 'John Jr.' 
```

[![Inheritance](img/1002d299facdc03a9b36d7697cdd83e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CPF8migm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h4127h6h69jlgeoaux0g.gif)

*   如果我们把约翰的名字删掉，由于继承的关系，它又会变成‘安’。
*   然后编辑安和约翰的名字。
*   最后，我们再次为约翰添加了自己的名字。

Easy State 会在每次操作后重新渲染应用程序，因为所有这些操作都会产生不同的视图。

## 结论

上面的例子展示了基本的 JavaScript 操作和模式，大多数人甚至没有考虑到状态管理。我认为部分原因是第三方状态管理 API 增加了复杂性。

通过使用 Easy State，您可以将您的大脑容量集中在发明您个人的、可重用的和纯粹的 JavaScript 模式上，而不是学习新的 API 和概念。

如果这篇文章引起了你的兴趣，请分享出来。还可以看看[易邦回购](https://github.com/solkimicreb/react-easy-state)并在离开前留下一颗星。

*谢谢！*
*(本文原载于[中](https://medium.com/@solkimicreb/stress-testing-react-easy-state-ac321fa3becf) )*