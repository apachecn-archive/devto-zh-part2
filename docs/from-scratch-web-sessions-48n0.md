# 从头开始:Web 会话

> 原文：<https://dev.to/absolux/from-scratch-web-sessions-48n0>

大家好，

作为第一个贡献，我想和你分享我对网络会议的想法。让我们从定义什么是服务器端会话开始。然后，我将向您展示一个抽象类的示例，它以简单优雅的方式管理会话。

Web 会话是一个行业标准特性，它允许服务器应用程序在客户端应用程序(主要是浏览器)和服务器之间的多次请求/响应交互期间维护和存储用户特定的数据。

状态管理是 web 会话的主要目的，它们与 cookies、请求、响应、编程语言或框架没有关系。它们都有相同的成员和行为，可以用任何语言编写。

## 属性

基本且必需的会话属性可以是:

*   一个`state`，它是会话类中的主要属性。它可以是一个简单的保存键/值对的`Map`对象。
*   一个独特的`identifier`或`token`来区分一个会话和另一个会话。
*   一个`lifetime`或`time to live`(又名 TTL)属性，用于计算一段时间不活动后的到期时间。它对应于会话在到期前可以存活的秒数。
*   最后，用一个`storage`或任何属性名来存储持久性适配器。它将负责从存储器(文件系统中的文本文件、数据库或执行应用程序的内部存储器)中保存和检索会话数据。

## 方法

在面向对象编程中，成员和方法驻留在同一个类中。为了自治，会话实例必须:

*   保存其状态以备将来使用，
*   如果会话过期，则销毁数据，
*   初始化时从存储器中检索状态，
*   并在必要时重新生成新的标识符。

因此，我们的会话类可以具有以下结构:

```
abstract class Session {
  /**
   * A Map object to store key/value pairs
   */
  private state: Map

  /**
   * A string that uniquely identifies the session
   */
  private id: string

  /**
   * A number of seconds, milliseconds or even minutes to get the expiry time
   */
  private lifetime: number

  /**
   * Storage driver is responsible of the saving/retrieving the session state
   */
  private storage: StorageInterface

  /**
   * Start the session, loading the state from the storage
   */
  public async start (): boolean

  /**
   * End the session, saving the state in the storage
   */
  public async commit (): boolean

  /**
   * Regenerate a new session ID, maintaining the current state
   */
  public async regenerate (): boolean

  /**
   * Invalidate the session, removing or flagging the session as expired
   */
  public async invalidate (): boolean
} 
```

> 像`get()`、`set()`和`remove()`这样的方法...等等，都是操作会话状态所必需的，但是为了简单起见，我们将忽略它们。

我在代码示例中使用了`TypeScript`符号，并且在 [Github](https://github.com/aldojs/session) 上写了一个完整的实现，如果你想看实际操作的话。

## 储存

封装存储的持久性驱动程序也是精简和优化的。它只有一个责任。

```
interface StorageInterface {
  /**
   * Retrieve an entry by its key
   */
  read (key: string): any

  /**
   * Remove an entry by its key
   */
  remove (key: string): any

  /**
   * Save an entry's data for the given time
   */
  write (key: string, data: any, ttl: number): any
} 
```

## 包装完毕

这是 web 会话的一般概念及其存在的理由。它们提供了`functionality`(状态管理)而不是`usability`(创建、传输、编码或持久化)。

`Decorator`设计模式可用于向会话添加更多特性，而无需从头修改或重写。不幸的是，web 框架开发人员不断重复，用不同的 API 一遍又一遍地重新实现相同的会话逻辑。

* * *

链接

*   [https://machinesaredigging . com/2013/10/29/how-do-a-web-session-work/](https://machinesaredigging.com/2013/10/29/how-does-a-web-session-work/)
*   [https://F5 . com/resources/white-papers/cookies-sessions-and-persistence](https://f5.com/resources/white-papers/cookies-sessions-and-persistence)
*   [https://www . owasp . org/index . PHP/Session _ Management _ Cheat _ Sheet](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet)
*   [https://en . Wikipedia . org/wiki/Session _(computer _ science)](https://en.wikipedia.org/wiki/Session_(computer_science))