# 简单后台调度

> 原文：<https://dev.to/jermdavis/simple-background-scheduling-18pb>

我经常遇到需要一些简单的代码来在应用程序的后台安排一些工作。有时是因为服务(可能是集成任务)需要经常开始处理，或者有时是因为更大程序的一些后台部分需要与主执行并行发生。这些需求的一个共同点是任务应该经常运行，但是无论后台处理需要多长时间，任务的两个实例都不应该重叠。

有几次，我遇到了这个需求的实现被巧妙破坏的项目，所以我想我应该写下一个对我有用的简单方法。这样的话，下次我需要它的时候，我就不用去搜索 git repos 了…

偶尔运行一些代码意味着我们需要运行一些东西，以及运行它的时间表。因此，一个处理这种情况的类的开始可能看起来像:

```
using System;
using System.Threading;

public class FunctionScheduler
{
  private int _runEveryMs;
  private Action _actionToRun;

  public FunctionScheduler(int runEveryMs, Action actionToRun)
  {
    _runEveryMs = runEveryMs;
    _actionToRun = actionToRun;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要能够开始这个过程，并安排行动。这对于标准的 Windows 定时器 API 来说很简单:

```
private Timer _timer = null;

public void Start()
{
  _timer = new Timer(new TimerCallback(timerTick), null, 0, _runEveryMs);
}

private void timerTick(object o)
{
  _actionToRun?.Invoke();
} 
```

Enter fullscreen mode Exit fullscreen mode

所以每次 [`Timer`](https://msdn.microsoft.com/en-us/library/system.threading.timer(v=vs.110).aspx) 打勾的时候，动作就会被调用。(现在，您可以在这里做更多的事情来涵盖类似“如果`Start()`被调用两次会发生什么”这样的情况——但是您已经明白了)

所以下一步是考虑这个需求，以确保动作在被再次调用之前总是被允许完成执行。这就是我在过去的解决方案中看到许多代码失败的地方——实现复杂的代码来尝试和实现这一点。

但是在 Windows APIs 中有一个简单的解决方案。检查动作状态的一种简单的线程安全方法是使用 [`Monitor`对象](https://msdn.microsoft.com/en-us/library/system.threading.monitor(v=vs.110).aspx)。这可以“锁定”我们代码中的一个对象。如果锁在动作开始时被取出，在动作结束时被释放，我们就有一个简单而安全的方法来确保动作不会与自身并行运行。

```
private object _lock = new object();

private void timerTick(object o)
{
  if (Monitor.TryEnter(_lock))
  { 
    _actionToRun?.Invoke();
    Monitor.Exit(_lock);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果计时器在动作仍在运行时触发，那么它将无法获取锁，因此不会调用动作的第二个实例。

现在这里有一个缺陷——如果动作中断会发生什么？如果一个异常在动作执行过程中被抛出，但是你的应用没有退出，那么`Monitor`保持在“进入”状态。一旦发生这种情况，每次`Timer`触发动作都不会重新运行。当然，解决这个问题很简单:

```
private void timerTick(object o)
{
  if (Monitor.TryEnter(_lock))
  {
    try
    {
      _actionToRun?.Invoke();
    }
    finally
    {
      Monitor.Exit(_lock);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，不管这个动作发生了什么，一旦它完成了,`Monitor`锁就会被释放。

最后，我们可以在不再需要的时候停止计时器:

```
public void Stop()
{
  _timer.Dispose();
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就可以按计划触发我们的操作了…我不确定这是否是实现这一点的“最佳”方式，但这已经在我的家酿 RSS 阅读器中安排了大约五年的主要更新循环，并且似乎工作可靠。如果你想复制的话，有一个完整课程的要点。

当您有一个为流程生命周期安排的单个工作时，这种方法最有意义，但是有时您有更小的任务，您需要更频繁地开始和停止。在这种情况下，使用基于 dispose 的模式，代码的可读性可能会更好:

```
public class DisposableFunctionScheduler : IDisposable
{
  private int _runEveryMs;
  private Action _actionToRun;
  private Timer _timer = null;
  private object _lock = new object();

  public DisposableFunctionScheduler(int runEveryMs, Action actionToRun)
  {
    _runEveryMs = runEveryMs;
    _actionToRun = actionToRun;
    _timer = new Timer(new TimerCallback(timerTick), null, 0, _runEveryMs);
  }

  public void Dispose()
  {
    _timer.Dispose();
  }

  private void timerTick(object o)
  {
    if (Monitor.TryEnter(_lock))
    {
      try
      {
        _actionToRun?.Invoke();
      }
      finally
      {
        Monitor.Exit(_lock);
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以安排一些工作如下:

```
class Program
{
  static void Action()
  {
    // Do something useful here...
    Console.WriteLine("Action called at " + DateTime.Now.ToString());
  }

  static void Main(string[] args)
  {
    Console.WriteLine("Starting...");
    using (var fs = new DisposableFunctionScheduler(4000, Action))
    {
      // Do whatever other work needs to happen while
      // the scheduler works in the background...
      Console.ReadLine();
    }
    Console.WriteLine("Stopping...");
    Console.ReadLine();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

简单–现在记录下来，以便下次我意识到我需要这段代码时使用…