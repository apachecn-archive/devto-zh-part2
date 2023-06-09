# 用 ReplicationBuilder 复制 typescript 中的不可变数据

> 原文：<https://dev.to/_maimart_/replicate-immutable-data-in-typescript-with-the-replicationbuilder-4ah3>

[![ReplicationBuilder Demo](img/2f14f5270af341e2a6abed1857387457.png "ReplicationBuilder Demo")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QEU1qE9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://maimart.de/wp-content/uploads/2018/07/ReplicationBuilder.gif)

ReplicationBuilder 是一个类型脚本工具，以一种类型安全、可重构和可读性良好的方式克隆不可变的数据对象。

在我们的单页面应用程序中，我们经常使用 redux 的不同实现来处理我们的应用程序状态。

数据结构变得越复杂，使用 Object.assign()或类似的方法处理不可变状态的代码就变得越复杂。

因此，这通常会导致代码难以阅读和维护。

所以我的意图是创建一个工具，能够以一种类型安全、可重构和可读性良好的方式处理不可变数据。

Typescript 2.1 中引入了强大的 keyof 特性，以一种类型安全的方式查找对象属性，这在很大程度上帮助了我解决这个问题。

结果我称之为 ReplicationBuilder，它是一个类型安全、流畅的 API，用于构建对象的副本。

它和其他一些小助手一起发布在 npm 包“typescript-immutable-helper”中。

源代码托管在 [GitHub 库](https://github.com/maimArt/typescript-immutable-helper)上

我将很高兴得到一些反馈，或者如果你想作出贡献。

## 语法

#### 简单地用新值替换属性

```
return ReplicationBuilder.forObject(state).property('party')
   .replaceValueOf('name').with('MyParty')
   .build() 
```

Enter fullscreen mode Exit fullscreen mode

#### 根据旧值替换属性

```
return ReplicationBuilder.forObject(state).property('party')
   .replaceValueOf('partymemberArray').by((oldPartymemberArray) => [...oldPartymemberArray, 'new partymember'])
   .build() 
```

Enter fullscreen mode Exit fullscreen mode

#### 克隆属性并对其应用一些功能

```
return ReplicationBuilder.forObject(state)
   .replaceValueOf('party').withCloneAndDo((clonedParty) => clonedParty.addPartyMember('new partymember'))
   .build() 
```

Enter fullscreen mode Exit fullscreen mode

## 特征

#### 类型安全属性

[![](img/afa838d23c6851a99488d779070cb37c.png)T2】](http://maimart.de/wp-content/uploads/2018/07/typescript_immutable_helper_invalidProperty.png)

#### 键入安全属性值

[![](img/864c49857327d5f6d254c5682ed5c2d1.png)T2】](http://maimart.de/wp-content/uploads/2018/07/typescript_immutable_helper_invalidValue.png)

#### 可链接

```
return ReplicationBuilder.forObject(state)
    .property('party').replaceValueOf('name').with('MyParty').replaceValueOf('members').by((members) => [...members, newMember])
    .property('initiator').replaceValueOf('prename').with('Party').replaceValueOf('surname').with('guy')
    .build(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 可重构，易读

## 用法

1.  通过调用`ReplicationBuilder.forObject()`加载一个对象
2.  通过类型安全函数`property()`向下导航对象树
3.  使用任一方法修改属性(请参见上面的语法段落)
    *   `replaceValueOf('prop').with(newValue:T)`
    *   `replaceValueOf('prop').by((T) => newValue:T)`
    *   `replaceValueOf('prop').withCloneAndDo((clonedProp) => clonedProp.doSomething()`
    *   `removeProperty('prop')`删除结果对象中的属性
4.  重复步骤 3 和 4，直到完成所有修改
5.  用`build()`制作复制品

## 行为

*   深层复制源对象
*   冻结入->冻结出。如果源对象被冻结(用于检测开发过程中的操作)，则生成的副本也将被深度冻结。
*   如果源对象未深度冻结，则发出警告(生产的复制副本将被深度冻结)

## 演示

[![ReplicationBuilder Demo](img/2f14f5270af341e2a6abed1857387457.png "ReplicationBuilder Demo")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QEU1qE9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://maimart.de/wp-content/uploads/2018/07/ReplicationBuilder.gif)

最初发布于 [http://maimart.de](http://maimart.de)