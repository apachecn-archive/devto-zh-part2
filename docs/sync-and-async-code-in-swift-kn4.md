# Swift 中的同步和异步代码

> 原文：<https://dev.to/onmyway133/sync-and-async-code-in-swift-kn4>

[![](img/afa67faa34edfd8e25992ddbd67f4a86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K6LAkRcr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AHevKOVykUzRprufo)

我们应该使用 DispatchQueue 来构建线程安全代码。这样做是为了防止两个不同的线程同时发生两个读和写操作，这会导致数据损坏和意外行为。请注意，您应该尽量避免死锁[stack overflow . com/questions/15381209/how-do-I-create-a-deadlock-in-grand-central-dispatch](https://stackoverflow.com/questions/15381209/how-do-i-create-a-deadlock-in-grand-central-dispatch)

## 全部同步

使用 try catch 和串行队列。使用同步功能阻塞当前队列。

```
func getUser(id: String) throws -> User {

  var user: User!

  try serialQueue.sync {

    user = try storage.getUser(id)

  }

返回用户
 }

func setUser(_ User:User)throws {
try serial queue . sync {
try storage . setUser(User)
}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
全异步

使用结果，获得串行队列。使用异步函数返回当前队列。

```
enum Result<T> {

  case value(T)

  case error(Error)

}

func getUser(id: String，completion:(结果<user>)-Void){
try serial queue . async {
do {
user = try storage . getUser(id)
completion(。值(用户))
 } catch { 
完成(。错误(error)) 
 } 
 }</user>

返回用户
 }

func setUser(_ user: User，completion:(Result)-> Void){
try serial queue . async {
do {
try storage . setUser(User)
completion(。值(())
 } catch { 
完成(。错误(error)) 
 } 
 } 
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
同步读取，异步写入

使用 try catch 进行读取，使用 Result 进行写入，并使用并发队列。对读取使用 sync 函数来阻塞当前线程，而对写入使用带屏障标志的 async 函数来返回当前队列。这有利于在没有写操作的情况下优先选择多次读操作。当有障碍的写入进入队列时，其他操作必须等待。

```
func getUser(id: String) throws -> User {

  var user: User!

  try concurrentQueue.sync {

    user = try storage.getUser(id)

  }

返回用户
 }

func setUser(_ user: User，completion:(Result)--> Void){
try concurrent queue . async(flags:。{ 
做屏障{ 
试储. setUser(用户)
完成(。值(())
 } catch { 
完成(。错误(error)) 
 } 
 } 
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
异步测试

之前，我们可以使用 [dispatch_apply](https://developer.apple.com/documentation/dispatch/1453050-dispatch_apply) 向调度队列提交一个块以进行多次调用。从 Swift 开始，等价于[并发执行](https://developer.apple.com/documentation/dispatch/dispatchqueue/2016088-concurrentperform)

```
DispatchQueue.concurrentPerform(iterations: 1000) { index in

    let last = array.last ?? 0

    array.append(last + 1)

} 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
参考

*   [http://basememara.com/creating-thread-safe-arrays-in-swift](http://basememara.com/creating-thread-safe-arrays-in-swift/)

*   [oleb . net/blog/2013/07/parallelize-for-loops-gcd-dispatch _ apply](https://oleb.net/blog/2013/07/parallelize-for-loops-gcd-dispatch_apply/)

原帖[https://medium . com/fantageek/sync-and-async-code-in-swift-8 E6 BC 7 BD 5866](https://medium.com/fantageek/sync-and-async-code-in-swift-8e6bc7bd5866)

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️