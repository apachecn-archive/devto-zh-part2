# 传播运营商技巧

> 原文：<https://dev.to/robaguilera/spread-operator-tricks-4g9k>

[![I'm going to butter your bread](img/89970fb85629dc89446f3f926b5e53c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i47fieXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s5reg6x0xifp2qenwvqb.jpg)

我最近在使用 spread 操作符时发现了一些方便的模式，所以我想我应该写一篇博文分享一些。

在这篇博文中，我不打算介绍语法的基础知识。如果您需要复习， [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)是很好的资源。

### 不变

Javascript 对对象做了一些有趣的事情。每当您试图复制一个对象时，您可能会无意中引用它。

```
let oldNed = {
  name: 'Ned Stark',
  job: 'Warden of the North'
};
let newNed = oldNed;
newNed.job = 'Dead';
// oldNed variable is also mutated
console.log(oldNed) // { name: 'Ned Start', job: 'Dead' } 
```

数据突变会导致一些难以发现的错误，因此值得花费时间和精力来确保通过正确复制任何需要更改的数据来防止这种情况。一种方法是使用扩展运算符。

`let newNed = {...oldNed};`

对 newNed 的任何更改都不会改变 oldNed 变量。然而，有一个例外。spread 运算符不执行嵌套对象的深度克隆。

```
let oldNed = {
  name: 'Ned Stark',
  job: 'Warden of the North',
  kids: ['John', 'Rob', 'Bran', 'Sansa', 'Arya', 'Rickon']
};
let newNed = { ...oldNed };
newNed.kids.splice(5)
// oldNed is now also missing Rickon :( 
```

为了解决这个问题，你还必须展开嵌套数组

`let newNed = { ...oldNed, kids: [...oldNed.kids] };`

请记住，如果您有一个深度嵌套的对象，您可能需要某种自定义函数或库来帮助您进行深度克隆。

* * *

这里有一些其他漂亮的不可改变的技巧。

组合多个数组(片段或整体)。

```
let dontChangeMe = ['Apples', 'Peaches', 'Detergent', 'Flowers'];
let meNeither = ['A shiny red polo', 'coffee', 'milk'];
let shoppingList = [
    ...dontChangeMe,
    'diapers',
    ...meNeither.slice(1)
] 
```

复制对象并同时更新属性。

```
let nedStark = {
  name: 'Ned Stark',
  job: 'Warden of the North'
};
let newNed = { ...nedStark, job: 'Dead' }; 
```

将节点列表转换成实际数组。

```
var divs = document.querySelectionAll('div')
var arrayOfDivs = [...divs] // returns an array not a nodelist 
```

### Rest 运算符

所以，我个人还没有找到很多 Rest 操作符的用例。然而，我在 React 中使用 React-Router 创建认证路由时偶然发现了这种模式。这里有一个基本的例子。

```
const AuthenticatedRoute = ({ ...rest }) => {
  const id = this.state;
  if (!id) {
    return <Redirect to={{ pathname: '/home' }} />;
  }
  return <Route {...rest} />;
};
// In Use
<AuthenticatedRoute
  path='/dashboard'
  data={this.state.data}
  render={() => (
    <SomeComponent someProps={this.someProps} />
  )}
/> 
```

当您返回`<Route {...rest} />`时，rest 操作符的魔力就会发生。基本上，正在发生的是一个函数 AuthenticatedRoute 被调用，它检查状态对象上的 id。如果失败，它返回一个`<Redirect/>`组件。否则，它返回一个`<Route>`组件并通过它的所有道具(在这个例子中是`path`、`data`和`render`)。

很方便吧？还有吗？请在下面分享！