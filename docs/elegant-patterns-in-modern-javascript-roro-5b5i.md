# 现代 JavaScript 中的优雅模式:RORO

> 原文：<https://dev.to/billsourour/elegant-patterns-in-modern-javascript-roro-5b5i>

[![](../Images/b48b8091bed35b7f8efaa552d4eaf713.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mde8R_69--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ui7h5hll0p7z57474vd.jpg)

JavaScript 语言发明后不久，我就写了我的前几行代码。如果你当时告诉我，有一天我会写一系列关于 JavaScript 中的**优雅**模式的文章[的话，我会笑得把你赶出房间。我认为 JavaScript 是一种奇怪的小语言，几乎称不上“真正的编程”](https://medium.com/search?q=Elegant%20patterns%20in%20JavaScript)

从那以后的 20 年里发生了很多变化。我现在在 JavaScript 中看到了道格拉斯·克洛克福特写《T2 JavaScript:精彩部分》时看到的东西:“一种杰出的、动态的编程语言……具有巨大的表达能力。”

所以，事不宜迟，这是我最近在代码中使用的一个奇妙的小模式。我希望你能和我一样喜欢它。

***请注意*** *:我很确定这些都不是我发明的。我很可能在别人的代码中发现了它，并最终自己采用了它。*

## 接收一个对象，返回一个对象(RORO)

我的大多数函数现在接受类型为`object`的单个参数，其中许多函数也返回或解析为类型为`object`的值。

部分归功于 ES2015 中引入的*析构*功能，我发现这是一个强大的模式。我甚至给它起了一个愚蠢的名字“RORO ”,因为……品牌？🤷‍♂️

***注:*** *析构是我最喜欢的现代 JavaScript 特性之一。我们将在整篇文章中充分利用它，所以如果你不熟悉它，这里有一个来自 [Beau Carnes](https://www.youtube.com/playlist?list=PLWKjhJtqVAbk2qRZtWSzCIN38JC_NdhW5) 的快速视频来帮助你了解它。*

[https://www.youtube.com/embed/-vR3a11Wzt0](https://www.youtube.com/embed/-vR3a11Wzt0)

以下是您喜欢这种模式的一些原因:

*   命名参数
*   清洁剂默认参数
*   更丰富的返回值
*   简易功能组合

让我们来看看每一个。

## 命名参数

假设我们有一个函数返回给定角色的用户列表，假设我们需要提供一个选项来包含每个用户的联系信息，以及另一个选项来包含不活动的用户，传统上我们可以写:

```
function findUsersByRole (
  role,
  withContactInfo,
  includeInactive
) {...} 
```

Enter fullscreen mode Exit fullscreen mode

对这个函数的调用可能看起来像:

```
findUsersByRole(
  'admin',
  true,
  true
) 
```

Enter fullscreen mode Exit fullscreen mode

注意最后两个参数有多模糊。“真，真”指的是什么？

如果我们的应用程序几乎从不需要联系信息，但几乎总是需要不活跃的用户，会发生什么？我们不得不一直与这个中间参数作斗争，尽管它实际上并不相关(后面会有更多的介绍)。

简而言之，这种传统的方法留给我们的是潜在的模糊、嘈杂的代码，这些代码更难理解、更难编写。

让我们看看当我们接收到单个对象时会发生什么:

```
function findUsersByRole ({
  role,
  withContactInfo,
  includeInactive
}) {...} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们的函数看起来几乎一样，除了**我们在参数**周围放了大括号。这表明我们的函数现在期望一个具有属性名为`role`、`withContactInfo`和`includeInactive`的对象，而不是接收三个不同的参数。

这是因为 ES2015 中引入了一个名为*destructing*的 JavaScript 功能。

现在我们可以这样调用我们的函数:

```
findUsersByRole({
  role: 'admin',
  withContactInfo: true,
  includeInactive: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

这远没有那么模糊，而且更容易阅读和理解。另外，省略或重新排序我们的参数不再是一个问题，因为它们现在是一个对象的命名属性。

比如这个作品:

```
findUsersByRole({
  withContactInfo: true,
  role: 'admin',
  includeInactive: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

还有这个也是:

```
findUsersByRole({
  role: 'admin',
  includeInactive: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

这也使得在不破坏旧代码的情况下添加新参数成为可能。

这里需要注意的一点是，如果我们希望所有的参数都是可选的，换句话说，如果下面是一个有效的调用…

```
findUsersByRole() 
```

Enter fullscreen mode Exit fullscreen mode

…我们需要为我们的参数对象设置一个默认值，就像这样:

```
function findUsersByRole ({
  role,
  withContactInfo,
  includeInactive
} = {}) {...} 
```

Enter fullscreen mode Exit fullscreen mode

对我们的参数对象使用析构的一个额外好处是它促进了不变性。当我们在函数中析构`object`时，我们将对象的属性赋给新的变量。更改这些变量的值不会改变原始对象。

考虑以下情况:

```
const options = {
  role: 'Admin',
  includeInactive: true
}

findUsersByRole(options)

function findUsersByRole ({
  role,
  withContactInfo,
  includeInactive
} = {}) {
  role = role.toLowerCase()
  console.log(role) // 'admin'
  ...
}

console.log(options.role) // 'Admin' 
```

Enter fullscreen mode Exit fullscreen mode

即使我们改变了`role`的值，但是`options.role`的值保持不变。

值得注意的是，析构会生成一个 _ ***浅***_ 副本，因此如果我们的参数对象的任何属性是复杂类型(例如`array`或`object`)，更改它们确实会影响原始对象。

## 清洁剂默认参数

通过 ES2015，JavaScript 函数获得了定义默认参数的能力。事实上，我们最近在上面的`findUsersByRole`函数中将`={}`添加到参数对象时使用了一个默认参数。

使用传统的默认参数，我们的`findUsersByRole`函数可能如下所示。

```
function findUsersByRole (
  role,
  withContactInfo = true,
  includeInactive
) {...} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想将`includeInactive`设置为`true`，我们必须显式地将`undefined`作为`withContactInfo`的值来传递，以保持默认值，就像这样:

```
findUsersByRole(
  'Admin',
  undefined,
  true
) 
```

Enter fullscreen mode Exit fullscreen mode

多可怕啊。

将其与使用如下参数对象进行比较:

```
function findUsersByRole ({
  role,
  withContactInfo = true,
  includeInactive
} = {}) {...} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以写…

```
findUsersByRole({
  role: ‘Admin’,
  includeInactive: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

…并且我们的默认值`withContactInfo`被保留。

## 奖金:必选参数

你多久写一次这样的东西？

```
function findUsersByRole ({
  role,
  withContactInfo,
  includeInactive
} = {}) {
  if (role == null) {
    throw Error(...)
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

***注:*** *我们用上面的双等号(==)来测试用一条语句同时测试* `null` *和* `undefined` *。*

如果我告诉您，您可以使用默认参数来验证必需的参数，那会怎么样呢？

首先，我们需要定义一个抛出错误的`requiredParam()`函数。

像这样:

```
function requiredParam (param) {
  const requiredParamError = new Error(
   `Required parameter, "${param}" is missing.`
  )

  // preserve original stack trace
  if (typeof Error.captureStackTrace === ‘function’) {
    Error.captureStackTrace(
      requiredParamError,
      requiredParam
    )
  }

  throw requiredParamError
} 
```

Enter fullscreen mode Exit fullscreen mode

#### *我知道，我知道。requiredParam 不 RORO。这就是为什么我说* ***我的功能中有很多****——而不是所有***。**

 *现在，我们可以将对`requiredParam`的调用设置为`role`的默认值，比如:

```
function findUsersByRole ({
  role = requiredParam('role'),
  withContactInfo,
  includeInactive
} = {}) {...} 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的代码，如果任何人在没有提供`role`的情况下调用`findUsersByRole`，他们将得到一个表示`Required parameter, “role” is missing.`的`Error`

从技术上讲，我们也可以将这种技术与常规默认参数一起使用；我们不一定需要一个对象。但这一招太有用了，不能不提。

## 更丰富的返回值

JavaScript 函数只能返回一个值。如果这个值是一个`object`，它可以包含更多的信息。

考虑一个将`User`保存到数据库的函数。当该函数返回一个对象时，它可以向调用者提供大量信息。

例如，一种常见的模式是在保存函数中“向上插入”或“合并”数据。这意味着，我们将行插入数据库表(如果它们不存在)或更新它们(如果它们存在)。

在这种情况下，知道我们的保存函数执行的操作是`INSERT`还是`UPDATE`会很方便。获得数据库中存储内容的准确表示也是很好的，了解操作的状态也是很好的；它成功了吗？它作为一个更大的事务的一部分挂起了吗？它超时了吗？

当返回一个对象时，很容易一次传达所有这些信息。

大概是:

```
async saveUser({
  upsert = true,
  transaction,
  ...userInfo
}) {
  // save to the DB
  return {
    operation, // e.g 'INSERT'
    status, // e.g. 'Success'
    saved: userInfo
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从技术上讲，上面返回了一个解析为一个`object`的`Promise`,但是你明白了。

## 简易函数构图

> “功能组合是将两个或多个功能组合起来产生一个新功能的过程。将函数组合在一起就像是将一系列管道连接在一起，让我们的数据流过。”—埃里克·埃利奥特

我们可以使用类似于
的`pipe`函数来组合函数

```
function pipe(...fns) {
  return param => fns.reduce(
    (result, fn) => fn(result),
    param
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数获取一个函数列表，并返回一个可以从左到右应用该列表的函数，从给定的参数开始，然后将列表中每个函数的结果传递给列表中的下一个函数。

如果你感到困惑，不要担心，下面有一个例子可以让你明白。

这种方法的一个限制是列表中的每个函数只能接收一个参数。幸运的是，当我们滚的时候这不是问题！

这里有一个例子，我们有一个`saveUser`函数，它通过三个独立的函数来传递一个`userInfo`对象，这三个函数依次验证、规范化和持久化用户信息。

```
function saveUser(userInfo) {
  return pipe(
    validate,
    normalize,
    persist
  )(userInfo)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在我们的`validate`、`normalize`和`persist`函数中使用 [rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)来仅析构每个函数需要的值，并且仍然将所有内容传递回调用者。

这里有一小段代码可以告诉你它的要点:

```
function validate(
  id,
  firstName,
  lastName,
  email = requiredParam(),
  username = requiredParam(),
  pass = requiredParam(),
  address,
  ...rest
) {
  // do some validation
  return {
    id,
    firstName,
    lastName,
    email,
    username,
    pass,
    address,
    ...rest
  }
}

function normalize(
  email,
  username,
  ...rest
) {
  // do some normalizing
  return {
    email,
    username,
    ...rest
  }
}

async function persist({
  upsert = true,
  ...info
}) {
  // save userInfo to the DB
  return {
    operation,
    status,
    saved: info
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 要 RO 还是不要 RO，这是个问题

我在一开始就说过，**我的大多数**函数接收一个对象，**它们中的许多**也返回一个对象。

像任何模式一样，RORO 应该被看作是我们工具箱中的另一个工具。我们通过使参数列表更加清晰和灵活，以及使返回值更具表达性，在它增加价值的地方使用它。

如果你正在编写一个只需要接收一个参数的函数，那么接收一个`object`就太过分了。同样，如果您正在编写一个函数，它可以通过返回一个简单的值向调用者传达一个清晰直观的响应，那么就没有必要返回一个`object`。

一个我几乎从不出错的例子是断言函数。假设我们有一个函数`isPositiveInteger`来检查一个给定的参数是否是正整数，这样的函数可能根本不会从 RORO 中受益。

* * *

如果你喜欢这篇文章，请点击♥️图标帮助传播。如果你想阅读更多类似的东西，请注册下面的我的开发掌握时事通讯。

# [注册 DevMastery 简讯](https://upscri.be/83d379/)

我会保密你的信息，从不发送垃圾邮件。*