# AtomicReference，有时是同步块的更简单的替代方法

> 原文：<https://dev.to/vmlensd/atomicreference-a-sometimes-easier-alternative-to-synchronized-blocks-538c>

Brian Goetz 在他的书《实践中的 [Java 并发性](http://jcip.net/)的高级主题部分中列出了 [AtomicReference](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/AtomicReference.html) 。然而，我们将看到，对于特定的用例，AtomicReference 比 synchronized 块更容易使用。新的 JDK 8 getAndUpdate 和 updateAndGet 方法使 AtomicReference 更加易于使用。

但是让我们从第一个主题开始，一个通过 AtomicReference 比通过 synchronized 块更容易实现的用例:并发状态机。

# 如何使用 compareAndSet:一个并发状态机

来自 [maven surefire 插件](https://github.com/apache/maven-surefire)的类 [CommandReader](https://github.com/apache/maven-surefire/blob/master/surefire-api/src/main/java/org/apache/maven/surefire/booter/CommandReader.java) 使用 compareAndSet 方法实现并发状态机:

```
public final class CommandReader {
  private static final CommandReader READER = new CommandReader();
  private final Thread commandThread = 
    newDaemonThread( new CommandRunnable(), "surefire-forkedjvm-command-thread" );
  private final AtomicReference<Thread.State> state =
   new AtomicReference<Thread.State>( NEW );
  public static CommandReader getReader() {
     final CommandReader reader = READER;
     if ( reader.state.compareAndSet( NEW, RUNNABLE ) ) {
         reader.commandThread.start();
     }
   return reader;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

类 AtomicReference 包装了另一个类，用原子更新功能来丰富变量。在第 5 行中，AtomicReference 表示枚举类型 Thread.State 的原子变量。

当当前状态为新状态时，getReader 方法必须启动 commandThread，并将其值更新为 RUNNABLE。由于该方法可以由多个线程并行调用，因此设置和检查必须自动完成。这是通过 compareAndSet 方法第 9 行完成的。只有当当前值与预期值相同时，compareAndSet 方法才会将其值更新为新值。在本例中，它只在当前值为新值时将变量更新为 RUNNING。如果 updated succeed 方法返回 true，线程启动，否则返回 false，什么也不做。检查和更新是自动完成的。

作为比较，这里是用同步块实现的相同功能。

```
public final class CommandReader {
 private static final CommandReader READER = new CommandReader();
 private final Thread commandThread = 
   newDaemonThread( new CommandRunnable(), "surefire-forkedjvm-command-thread" );
 private final  Thread.State state =  NEW;
 private final Object LOCK = new Object();
 public static CommandReader getReader()  {
    final CommandReader reader = READER;
    synchronized(reader.LOCK) {
      if(reader.state == NEW) {
         reader.commandThread.start();
         reader.state = RUNNABLE;
      }
    }
    return reader;
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在变量 state 的检查和更新周围使用了一个 synchronized 块，第 10 行。这个例子说明了为什么我们需要自动检查和更新。如果没有同步，两个线程可能会多次从 commandThread 调用 start 方法来读取 state NEW。

正如我们看到的，我们可以通过一个对 compareAndSet 的方法调用来替换 synchronized 块、if 语句和对状态的写入。在下一个示例中，我们将看到如何使用 compareAndSet 方法来更新值。

# 更新值:重试直到成功

使用 compareAndSet 进行更新的想法是重试，直到更新成功。RXJava 中的 AsyncProcessor 类使用这种技术来更新 add:
方法中的订阅者数组

```
final AtomicReference<AsyncSubscription<T>[]> subscribers;
boolean add(AsyncSubscription<T> ps) {
 for (;;) {
   AsyncSubscription<T>[] a = subscribers.get();
   if (a == TERMINATED) {
     return false;
   }
   int n = a.length;
   @SuppressWarnings("unchecked")
   AsyncSubscription<T>[] b = new AsyncSubscription[n + 1];
   System.arraycopy(a, 0, b, 0, n);
   b[n] = ps;
   if (subscribers.compareAndSet(a, b)) {
     return true;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 for 循环重试更新，第 3 行。仅当订户数组处于终止状态(第 6 行)或者 compareAndSet 操作成功(第 14 行)时，循环才终止。在所有其他情况下，更新会在阵列的副本上重复进行。

从 JDK 8 开始，类 AtomicReference 在两个实用程序方法 getAndUpdate 和 updateAndGet 中提供此功能。下面显示了 JDK 8 中 getAndUpdate 方法的实现:

```
public final V getAndUpdate(UnaryOperator<V> updateFunction) {
 V prev, next;
 do {
   prev = get();
   next = updateFunction.apply(prev);
 } while (!compareAndSet(prev, next));
 return prev;
} 
```

Enter fullscreen mode Exit fullscreen mode

该方法使用的技术与 AsyncProcessor 类中的 add 方法相同。它在循环中重试 compareAndSet 方法，第 6 行。当更新失败时，将多次调用 updateFunction。所以这个函数必须是无副作用的或者幂等的。

下面是上面用新的 updateAndGet 方法实现的 add 方法:

```
boolean add(AsyncSubscription<T> ps) {
AsyncSubscription<T>[] result = subscribers.updateAndGet(  ( a ) ->  {  
  if (a != TERMINATED) {       
    int n = a.length;
    @SuppressWarnings("unchecked")
    AsyncSubscription<T>[] b = new AsyncSubscription[n + 1];
    System.arraycopy(a, 0, b, 0, n);
    b[n] = ps;
    return b;
  }
  else {
    return a;
  }
});
return result != TERMINATED;    
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们看到的，while 循环隐藏在 updateAndGet 方法中。我们只需要实现一个从旧值计算新值的函数。

# 结论和下一步

我们已经看到了 compareAndSet 的两个例子。如果你对更多的例子感兴趣，看看《多处理器编程的艺术》这本书。它向您展示了如何使用 compareAndSet 实现典型的并发数据结构。[这篇文章展示了如何测试原子更新。](http://vmlens.com/articles/cp/examples_atomicReference/)

我将很高兴听到您如何在应用程序中使用 AtomicReference。