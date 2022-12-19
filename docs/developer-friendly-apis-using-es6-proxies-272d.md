# 使用 ES6 代理的开发人员友好的 API

> 原文：<https://dev.to/khaled_garbaya/developer-friendly-apis-using-es6-proxies-272d>

[![book-1659717 1920](../Images/5e613b51922a1056fb3307e921d58999.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h-6xxrDy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/3bc97k4uk5q7/SDyuqsnJkIiYU86AmIkuw/a571a5c56eb71558bec1baf91d46febc/book-1659717_1920.jpg)

我尝试了很多 javascript 的新特性，并且总是试图为它们找到一个真正的用例。最近我是代理的忠实粉丝，让我感兴趣的是拦截对对象属性的访问并改变其行为的能力。

## 什么是代理？

代理是一个非常有趣的 es6 特性，它允许您在目标对象中访问属性时确定行为

## 它是如何工作的？

```
var p = new Proxy(target, handler) 
```

Enter fullscreen mode Exit fullscreen mode

**处理程序:**一个对象，它的属性是定义当操作在其上执行时代理的行为的函数。

**目标:**可以是任何种类的对象，或者另一个代理

您为想要拦截的每个操作定义`traps`,并定义行为。您可以为以下对象设置陷阱:

*   `get`
*   `set`
*   `getPrototypeOf`
*   `setPrototypeOf`
*   `isExtensible`
*   `preventExtensions`
*   `getOwnPropertyDescriptor`
*   `defineProperty`
*   `has`
*   `deleteProperty`
*   `ownKeys`
*   `apply`
*   `construct`

陷阱基本上是一个 _(ツ)_/函数。

这是可选的，如果你不定义它们，操作会被转发到目标，也就是`No-op forwarding proxy`

## 可以用它做什么？

如果你拦截你的用户对一个对象的访问，并确保他们在做正确的事情，让你的库更容易出错，或者给你的库应该如何使用提供更好的指导。此外，如果您希望更改数据的结构，并且不希望中断使用该结构的其他服务，该怎么办呢？在这篇博文中，我将尝试涵盖两个场景，在这两个场景中，您可以使用 javascript 代理、数据迁移和构建一个防错库。

## 数据迁移

数据结构很难从一开始就正确，因此在应用程序的整个生命周期中，您可能希望更改数据结构，比如删除不必要的属性，或者将一些属性组合在一起，这样的列表还会继续下去...我自己也经历过很多次。取决于您的应用程序的结构，您可能有一个作为数据提供者的服务和多个作为数据消费者的服务。一旦应用程序增长很多，改变数据结构可能会很可怕，容易出错，或者你没有能力重构你所有的服务。代理将确保您的数据消费者将总是得到他们所要求的，直到您有时间重构它们，并且一旦您的所有代码都准备好进行更改，您就可以取出代理对象。

## 让我们构建我们的《一千零一夜》应用程序

我们首先从我们的数据服务开始，它向其他服务提供数据，我们将其命名为`ScheherazadeDB`，它以一种“超级安全的方式”公开用户数据。作为第一次迭代，用户数据结构将如下所示。

```
const user = {
  username: 'alibaba',
  password: 'open sesame',
  address: 'Far far away'
  firsName: 'Ali',
  lastName: 'Baba',
  age: '44'
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们的应用程序中，我们有`ScheherazadeDB`消费者，尤其是`Shahryar`，让我们的处境更加艰难的是，这将在接下来的一千零一夜中每天晚上运行，直到天亮，消耗所有数据，如果数据提供商`ScheherazadeDB`未能提供预期的数据，消费者将决定该应用程序不再值得使用，并将终止该应用程序及其所有进程。

最初的几个晚上进行得非常好，直到团队中有人决定用户数据结构太扁平，我们应该做一些分组，比如在`personalInfos`组旁边放上名字、姓氏和年龄。我们如何才能在不影响跑步消费者的情况下做出改变？

救援的代理对象。让我们从我们需要的新数据结构开始。

```
const user = {
  username: 'alibaba',
  password: 'open sesame',
  address: 'Far far away'
  personalInfos: {
    firsName: 'Ali',
    lastName: 'Baba',
    age: '44'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是，没有一个消费者知道新的结构，他们会试图获取或设置不再存在的属性，如 firstName，我们的应用程序将会死亡，让我们在我们邪恶的消费者再次开始运行之前解决这个问题。使用代理对象，我们可以将 get 和 set 重定向到正确的属性，因此如果消费者请求`user.firstName`，我们可以返回`user.personalInfos.firstName`。

我们的代理对象看起来像这样

```
function proxyUser (user) {
  const handler = {
      get: (target, prop) => {
        // if the key is groupped we redirect it to there
        if (prop in target.personalInfos) {
          return target.personalInfos[prop]
        }
        // default access
        return target[prop]
      },
      set: (target, prop, value) => {
        // we do the same for set
        // but here we need to return because we don't want to set
        // the value twice in the group and in the object
        if (prop in target.personalInfos) {
          target.personalInfos[prop] = value
          // on a set trap we need to return true to mark the set as successful
          return true
        }
        target[prop] = value
        return true
      }
    }
  const proxy = new Proxy(user, handler)
  return proxy
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要做的唯一一件事就是修改我们的`cheherazadeDB`服务，返回 proxyUser，而不是原来的用户对象。

```
export async function getUser (id) {
  const user = await selectSingleUser({where: {$id: id}})
  // --- previously ---
  //return user
  //--- now ---
  const proxiedUser = proxyUser(user)
  return proxiedUser
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在我们的服务运行得很好，我们需要警告开发人员一些字段已经过时，他们应该使用新的属性。让我们修改我们的`proxyUser`函数来反映这一点。

```
function proxyUser (user) {
  const handler = {
      get: (target, prop) => {
        // if the key is groupped we redirect it to there
        if (prop in target.personalInfos) {
          // NEW: Add deprecation warning
          console.warn(`Accessing ${prop} directly will be deprecated in the next version please use myObject.personalInfos.${key} instead`)
          return target.personalInfos[prop]
        }
        // default access
        return target[prop]
      },
      set: (target, prop, value) => {
        // we do the same for set
        // but here we need to return because we don't want to set
        // the value twice in the group and in the object
        if (prop in target.personalInfos) {
          // NEW: Add deprecation warning
          console.warn(`Accessing ${prop} directly will be deprecated in the next version please use myObject.personalInfos.${key} = value instead`)
          target.personalInfos[prop] = value
          // on a set trap we need to return true to mark the set as successful
          return true
        }
        target[prop] = value
        return true
      }
    }
  const proxy = new Proxy(user, handler)
  return proxy 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当服务试图使用数据时，就会收到警告，例如

```
//....
  const user = await getUser('<id>')
  console.log(user.firstName) 
  //... 
```

Enter fullscreen mode Exit fullscreen mode

我们将在控制台中得到这个输出。[![console-wwarning](../Images/583d8e3e4ad49276addffc93a8fcc717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h8JLjI53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.contentful.com/3bc97k4uk5q7/5nlgO5eOKkMKKy0QC266oi/c66c5ac3e1028952f2db18d2e943e14e/console-wwarning.png)

> 就这样，服务消费者日复一日地活着，因为他急切地期待着前一天晚上故事的结束。一千零一夜和一千个故事结束时，山鲁佐德告诉国王，她没有更多的故事要告诉他了。在这一千零一夜中，国王爱上了山鲁佐德。他饶了她一命，立她为王后。

## 现在可以用了吗？

是的，因为大多数主流浏览器已经支持它了。

[![Proxy object browser support](../Images/3acc628a8d33597b863699fbcff3b9dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jw6Nkgdj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.contentful.com/3bc97k4uk5q7/65RsHTojIseQYaiAmi6oa8/cdf5858c7c03395aa0d158c729fc997d/Proxy_object_browser_support.png)

Nodejs 也一样，因为完全支持 6.9 版代理对象

[![Proxy object nodejs support](../Images/3e455dc43cf440d938edc1545065158f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_F9O-Qq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.contentful.com/3bc97k4uk5q7/4lbxNUYrDOASkACAKkoQAe/5be0f42ca7b57f3dbdec0cebe761f361/Proxy_object_nodejs_support.png)

## 还能做什么？

使用代理对象的可能性是无限的，您可以限制对某些属性的写访问、日志访问等等。ponyfoo 有一篇很有深度的文章,你应该去看看。我还做了一个关于代理的演示，你可以在这里找到