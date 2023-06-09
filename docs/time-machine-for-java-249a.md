# Java 的时间机器

> 原文：<https://dev.to/devexperts/time-machine-for-java-249a>

本文描述了一个开发来支持 Java 应用程序中时间相关逻辑的单元测试的工具。该工具有助于控制交易平台和其他复杂和/或并发系统的质量。

为依赖于时间的功能编写单元测试并不总是简单的。在简单的情况下，简单地用自定义实现[【1】](#footnote)[【2】](#footnote)替换返回当前时间的方法就可以了。

这里有一个简单的方法来计算世界末日前的天数:

```
fun daysBeforeDoom() {
    return doomTime - System.currentTimeMillis()) / millisInDay
} 
```

用现有的工具[【1】](#footnote)[【2】](#footnote)或者通过使用 ASM 框架[【3】](#footnote)或 AspectJ[【4】](#footnote)编写特定于系统的代码转换器来替换所有的`System.currentTimeMillis()`调用通常就足够了。

在其他情况下，这种方法是不够的。假设我们希望我们的代码每天醒来并显示“离世界末日还有 N 天”,如下面的代码片段所示。

```
while (true) {
    Thread.sleep(ONE_DAY)
    println(“${daysBeforeDoom()} days left till the doomsday”)
} 
```

我们如何测试这段代码？我们如何断言它每天都被调用并显示正确的消息？使用上面描述的简单方法并替换`System.currentTimeMillis()`只允许我们测试消息的正确性。我们需要等待一整天来测试时间跨度。

如果没有额外的工具，测试这样的代码几乎是不可能的。让我们试着创建一个。

目前我们有两种方法返回当前时间:`System.currentTimeMillis()`和`System.nanoTime()`。我们还有几个依赖于时间的方法，它们等待一个事件或者可能超时:`Thread.sleep()`、`Object.wait()`和`LockSupport.park()`。

我们想要创建一个新的方法`increaseTime()`，它可以改变当前时间并唤醒任何等待超时的线程。

为了实现这一点，所有现有的依赖于时间的方法都需要用定制的实现来替换。让我们看看这是如何工作的..

**测试示例:**

```
increaseTime(ONE_DAY)
checkMessage() 
```

这在我们的检查消息和完成打印操作的实际时间之间创建了一个潜在的竞争条件。当然，一种方法是添加一个停顿:

**修复测试:**

```
increaseTime(ONE_DAY)
Thread.sleep(500 /*ms*/)
checkMessage() 
```

在常规实践中，这个测试几乎总是有效的，但是不能保证在消息打印出来后会调用`checkMessage()`。这可能是由于测试逻辑的复杂性或者仅仅是在过量使用的服务器上执行代码而导致的。您可能很想增加超时时间，但是这会使测试变慢，并且仍然不能保证正确性。

相反，我们需要一个特殊的方法来等待，直到所有被唤醒的线程都已完成。

**我们想要编写的测试:**

```
increaseTime(ONE_DAY)
waitUntilThreadsAreFrozen(1_000/*ms, timeout*/)
checkMessage() 
```

固有的挑战是，我们希望在测试过程中支持所有依赖于时间的方法，但我们也希望有一个`waitUntilThreadsAreFrozen()`方法，同时支持两者并不简单。

我们的解决方案是在 Devexperts 的一个特殊工具中实现的，用于测试依赖于时间的逻辑，称为 time-test[【5】](#footnote)。

让我们看看它是如何工作的。

Time-test 是作为 Java 代理实现的。要使用它，您应该添加 javaagent:timetest.jar 并将其包含在类路径中。该工具转换字节码，并用我们的特定实现替换所有依赖于时间的方法调用。然而，编写一个好的 java 代理有时并不简单，所以我们开发了一个 JAgent 框架[【6】](#footnote)来简化 java 代理的开发。

当创建依赖于时间的测试时，您应该启用 TestTimeProvider。它实现所有必需的依赖于时间的方法(`System.currentTimeMillis()`、`Thread.sleep()`、`Object.wait()`、`LockSupport.park()`、…)，并覆盖它们的默认实现。在大多数测试中，您实际上不需要管理底层时间，因此该工具在内部继续利用包装在重载方法中的默认时间相关方法。启动 TestTimeProvider 后，您可以使用`TestTimeProvider.setTime()`、`TestTimeProvider.increaseTime()`和`TestTimeProvider.waitUntilThreadsAreFrozen()`方法。

**TimeProvider.java:**

```
long timeMillis();
long nanoTime();
void sleep(long millis) throws InterruptedException;
void sleep(long millis, int nanos) throws InterruptedException;
void waitOn(Object monitor, long millis) throws InterruptedException;
void waitOn(Object monitor, long millis, int nanos) throws InterruptedException;
void notifyAll(Object monitor);
void notify(Object monitor);
void park(boolean isAbsolute, long time);
void unpark(Object thread); 
```

如前所述，TestTimeProvider 实现的主要挑战是同时支持依赖于时间的方法和附加的`waitUntilThreadsAreFrozen()`方法。每次更改时，所有需要的线程都被标记为恢复，然后才被唤醒。同时，`waitUntilThreadsAreFrozen()`等待，直到所有线程都处于等待状态，并且没有一个被标记为恢复。使用这种方法，线程将唤醒，重置它们的恢复标记，执行它们的任务，然后在 waitUntilThreadsAreFrozen 将它识别为完成之前返回到等待状态。

**使用 TestTimeProvider 进行测试:**

```
@Before
public void setup() {
    // Use TestTimeProvider for this test
    TestTimeProvider.start(/* initial time could be passed here */);
}

@After
public void reset() {
    // Reset time provider to default after the test execution
    TestTimeProvider.reset();
}

@Test
public void test() {
    runMyConcurrentApplication();
    TestTimeProvider.increaseTime(60_000 /*ms*/);
    TestTimeProvider.waitUntilThreadsAreFrozen(1_000 /*ms*/);
    checkMyApplicationState();
} 
```

我们希望时间测试工具能让你的生活更轻松。该工具是开源的，可以在 GitHub[【5】](#footnote)上获得。

测试愉快！

**参考文献:**
【1】[https://github.com/TOPdesk/time-transformer-agent/](https://github.com/TOPdesk/time-transformer-agent/)
【2】[https://stack overflow . com/questions/2001 671/override-Java-system-currenttimemillis-for-testing-time-sensitive-code](https://stackoverflow.com/questions/2001671/override-java-system-currenttimemillis-for-testing-time-sensitive-code)
【3】[http://asm.ow2.org/](http://asm.ow2.org/)
【4】[https://www.eclipse.org/aspectj/](https://www.eclipse.org/aspectj/)

由 Nikita Koval

在 Devexperts 担任并发性和代码分析问题的研究工程师