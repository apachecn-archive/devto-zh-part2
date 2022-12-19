# 异步/等待我们其余的人

> 原文：<https://dev.to/jamesmh/asyncawait-for-the-rest-of-us-c8a>

*注:[见我博客](https://www.blog.jamesmichaelhickey.com/async-await-for-the-rest-of-us/)的规范帖子 T3】*

C#中的`async`和`await`是怎么回事？为什么一个 2018 年的. Net 开发者**需要**知道它是什么，怎么用？

也许你已经使用过`async/await`但是发现自己不得不回去重新计算？没关系——无可否认，我也不是一个`async/await`大师。

我不得不艰难地发现，例如，使用`ConfigureAwait(false).GetResult()`(正如许多人可能建议的那样)并不能神奇地让你的异步方法同步“工作”。

但是这并不是对`async/await`内部以及如何使用`async/await`的深入研究。

这是*“异步/等待我们其余人”*。谁是*我们剩下的*？

我们:

*   可能从来没用过`async/await`
*   不是微软的大师
*   不是 C#大师
*   诚然，有时会忘记如何正确使用`async/await`。
*   可能要回到 Google，弄清楚为什么一个方法可以返回一个`Task`却不能使用`async`关键字。
*   可能想知道为什么被标记为`async`的**而不是**的方法可以被它的调用者等待

我希望这是一篇为“我们其余的人”写的文章，切中要害，切实可行。

*附:如果你确实想深入了解这个话题，最好的起点是[我建议从这篇文章开始。](https://blog.stephencleary.com/2012/02/async-and-await.html)T3】*

# 为什么要异步？

使用`async/await`有什么好处？

## 面向 Web 开发者

如果您使用。答案很简单:**可伸缩性**。

当您进行 IO 调用时——数据库查询、文件读取、从 HTTP 请求中读取等等。-正在处理当前 HTTP 请求的线程**正在等待**。

就是这样。只是在等待操作系统返回一个结果。

例如，执行数据库查询最终会要求操作系统连接到数据库，发送一条消息并得到一条消息作为回报。但这是操作系统发出的请求，而不是你的应用程序。

木卫一需要时间。等待线程(在你的应用中)可以用来做其他事情的时间——特别是**处理其他 HTTP 请求**。

> 使用`async/await`允许您的。Net web 应用程序能够在等待 IO 完成的同时处理更多的 HTTP 请求。

## 面向桌面/应用开发者

但是桌面应用程序不处理 HTTP 请求...

桌面应用程序**处理用户输入(键盘、鼠标等)。)、动画等。而且只有**一个 UI 线程**来做这件事。**

### 用户输入就是用户输入

如果我们认为 web 应用程序中的 HTTP 请求*只是用户输入*，桌面(或应用程序)键盘和鼠标事件*只是用户输入*——那么这对桌面/应用程序开发人员来说实际上更糟糕！他们只得到**一个线程**来处理用户输入！

IO 问题和修复仍然适用。

然而，CPU 密集型任务的问题是另一个问题。简而言之，这些类型的任务不应该在 UI 线程上完成。

任务类型包括:

*   在循环中处理大量项目
*   为报告计算聚合

如果你的应用程序这样做(在主/UI 线程上)，那么就没有什么可以处理用户输入和 UI 的东西，比如动画等等。

这导致冻结和滞后的应用程序。

解决方案是将 CPU 密集型任务卸载到后台任务/线程。这开始涉及到新线程/任务的排队，如何使用`ConfigureAwait(false)`在非 UI 上下文中保持代码的异步分支，等等。所有超出我们文章范围的事情。

# 异步关键字

让我们开始看看`async/await`关键词以及如何使用它们。

async 关键字引起了混淆。为什么？因为看起来**让**成为了你的异步方法。但是，事实并非如此。

那是令人困惑的。关键字**不会使我的方法异步**？没错。

## 那它做什么呢？

`async`关键字所做的就是**启用 await 关键字**。就是这样。仅此而已。它不做任何其他事情。

所以，就把`async`关键词想象成`enableAwait`关键词。

# Await 关键字

关键字`await`是奇迹发生的地方。它基本上是说(对代码的读者):

> 我(线程)将确保如果这里发生了异步的事情，我将去做别的事情(比如处理 HTTP 请求)。一旦异步工作完成，未来的一些线程将回到这里。

一般来说，`await`最常见的用法是当你在做类似 IO 的事情时，比如从数据库查询中获取结果或者从文件中获取内容。

当你用一个方法做 IO 时，做 IO 的不是你的应用程序，而是操作系统。所以你的线就在那里...等待...

会告诉当前线程离开并做一些有用的事情。让操作系统和。Net framework 会在需要的时候获取另一个线程。

请将此视为视觉指南:

```
var result1 = await SomeAsyncIO1(); // OS is doing IO while thread will go do something else.
// A thread gets the results.

var result2 = await SomeAsyncIO2(result1); // Thread goes to do something else.
// One comes back...

await SomeAsyncIO3(result2); // Goes away again...
// Comes back to finish the method. 
```

Enter fullscreen mode Exit fullscreen mode

此时你可能会问自己:

> 如果关键字`async`不能使一个方法异步，那么什么能呢？

## 那么是什么让一个方法异步呢？

嗯——这不是我们所学的`async`关键词。去想想。

使用`await`关键字可以等待任何返回“可获得的”——`Task`或`Task<T>`的方法。

实际上还有其他类型的奖励。并且,“可适应的”方法并不一定是异步方法。但是忽略这一点——这是为“我们其他人”准备的

出于本文的目的，我们假设“异步方法”是返回`Task`或`Task<T>`的方法。

### 这是什么时候的事？

我们什么时候需要从方法中返回一个`Task`？一般是做 IO 的时候。大多数 IO 库还是内置的。Net APIs 将有一个“异步”版本的方法。

例如，`SqlConnection`类有一个将开始连接的`Open`方法。但是，它也有一个`OpenAsync`方法。它还有一个`ExecuteNonQueryAsync`方法。

```
public async Task<int> IssueSqlCommandAsync() {
    using(var con = new SqlConnection(_connectionString))
    {
        // Some code to create an sql command "sqlCommand" would be here...
        await con.OpenAsync();
        return await sqlCommand.ExecuteNonQueryAsync();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使`OpenAsync`和`ExecuteNonQueryAsync`方法异步的是**而不是`async`关键字**，而是它们**返回一个`Task`或`Task<T>`T8。**

# 异步一路下行

有可能这样做(注意缺少`async`和`await` ):

```
public Task<int> GetSomeData() {
    return DoSomethingAsync();
} 
```

Enter fullscreen mode Exit fullscreen mode

然后`await`那个方法:

```
// Inside some other method....
await GetSomeData(); 
```

Enter fullscreen mode Exit fullscreen mode

`GetSomeData`不会等待对`DoSomethingAsync`的调用——它只会返回`Task`。记住`await`并不关心一个方法是否使用了`async`关键字- **，它只是要求这个方法返回一个`Task`** 。

这样做是可能的——创建一个调用异步方法但不等待的方法。

## 这是最佳做法

然而，这被认为是一种不好的做法。为什么？

既然这篇文章被认为是中肯而实用的:

使用 async/await“一路向下”只是更好地捕捉异步方法中的异常。

如果你用`async`关键字标记每个返回`Task`的方法——这反过来启用了`await`关键字——它可以更好地处理异常，并使它们在查看异常消息和堆栈跟踪时更容易理解。

# 结论

简单总结一下:

*   `async`关键字并不使方法异步——它只是启用了`await`关键字。

*   如果一个方法返回一个`Task`或`Task<T>`，那么它可以被`await`关键字用来管理我们代码的异步细节。

*   执行 IO 总是会导致线程阻塞。这意味着你的网络应用程序不能并行处理大量的 HTTP 请求，冻结和延迟应用程序。

*   使用`async/await`帮助我们创建代码，允许我们的线程在执行 IO 时停止阻塞并做有用的工作。

*   这使得网络应用程序每秒可以处理更多的请求，并且对用户的响应更快。

希望这是一个可以理解的`async/await`介绍。这不是一个简单的话题——和往常一样——随着时间的推移，通过使用这个特性获得经验将有助于我们理解正在发生的事情。

围绕`async/await`有太多要说的和太多的概念。其中包括:

*   什么是`SynchronizationContext`？我应该在什么时候意识到这一点？
*   关于。Net Core 和. Net Framework——有什么区别吗？
*   为什么我可以将一个方法标记为`async void`？这是做什么的？我应该这样做吗？
*   如何将 CPU 密集型工作卸载到后台线程/任务？
*   有没有可能在后台线程上工作，然后在最后返回到 UI 线程？
*   如何从同步代码中调用标有`async`关键字的方法？当我这样做的时候会发生什么？

让我知道你的想法——或者我是否错过了什么等等。谢谢！

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！