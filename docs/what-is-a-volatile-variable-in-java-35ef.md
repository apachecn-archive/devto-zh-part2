# Java 中的 Volatile 变量是什么？

> 原文：<https://dev.to/rnowif/what-is-a-volatile-variable-in-java-35ef>

`volatile`关键字是 Java 语言中不太为人所知和理解的关键字之一。本文的目标是解释它是什么以及何时使用它。

## 内存架构

为了理解`volatile`的值，首先必须理解计算机的内存架构:

[![](img/58b931474902d8924972d7dc254ee6bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LawCJKjx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rnowif.github.img/memory_architecture.png)

每个 CPU 都包含自己的寄存器，这些寄存器基本上是 CPU 内存。在这里访问这些寄存器并对变量执行操作非常快。每个 CPU 也有缓存层。它可以快速访问该高速缓存，但不如寄存器快。最后是主存(也叫 RAM)。所有的 CPU 都可以访问这个内存。主存储器比 CPU 的高速缓冲存储器大得多。

通常，当 CPU 需要从内存中读取某些内容时，它会将其读取到 CPU 缓存中，并对其执行操作。它甚至可以将它读入寄存器。当 CPU 需要将其写回内存时，它会将其寄存器刷新到高速缓冲存储器，最终，该高速缓冲存储器将被刷新回主内存。

一般来说，刷新是在 CPU 需要为其他信息腾出空间时执行的。因此，似乎很清楚，人们不能对*何时*发生这种冲刷做出任何假设。

## 共享变量的可视性

如果一台计算机有 2 个或更多的 CPU，它将能够同时运行几个线程。这意味着，如果每个线程都想访问同一个变量，每个 CPU 都会将该变量的“副本”复制到其缓存中，这可能会导致一个重大的同步问题。

以这段代码为例:

```
public class MyServer implements Runnable {
    private boolean running = true;

    @Override
    public void run() {
        while (running) {
            // Do some very interesting stuff...
        }
    }

    public void stop() {
        running = false;
    }
} 
```

很有可能从不同于执行`run`方法的线程调用`stop`方法。如果这些线程在两个不同的 CPU 上执行，`run`方法不会停止，直到 CPU 将其缓存刷新到主内存。我们发现没有办法预测这种情况何时会发生。

在 Java 中，`volatile`关键字明确要求将一个变量直接写入主存，这样所有 CPU 都可以立即使用它。

然后代码变成:

```
public class MyServer implements Runnable {
    private volatile boolean running = true;

    @Override
    public void run() {
        while (running) {
            // Do some very interesting stuff...
        }
    }

    public void stop() {
        running = false;
    }
} 
```

注意，可变变量可以是原语或对象，也可以是`null`。

## 什么时候用`volatile`

volatile 变量保证了它的最新版本总是可以从每个 CPU 获得。它通常用于由一个线程修改并由另一个线程读取的标志(如前面代码片段中的`running`布尔值)。从全局来看，如果变量可以被几个线程访问，并且你不需要以原子的方式执行一系列操作，你可以使用一个`volatile`变量(例如，你不应该增加一个`volatile`变量)。如果需要这样做，应该考虑使用同步机制。

## 结论

很少使用`volatile`关键字，可能是因为在 JEE(或 Spring)应用程序中弄乱线程是不被允许的，但是知道它并理解它的用法是非常有用的。事实上，它可以使您的代码更安全，并防止您编写依赖于锁和同步的算法，而这并不是真正需要的。