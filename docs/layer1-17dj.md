# 第 1 层

> 原文：<https://dev.to/jeikabu/layer1-17dj>

我已经介绍了“layer 0”，这是一个 Windows 服务，它以登录用户的身份生成并监控一个进程——“layer 1”。只说第一层才有意义。

## 会话事件

我们希望检测用户何时注销，这样我们可以让第 1 层干净地退出，而第 0 层不会试图重新启动它。这有两个部分:

1.  检测到用户会话即将结束
2.  返回一个退出代码为 [`GetExitCodeProcess()`](https://docs.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-getexitcodeprocess)

```
static L1ExitCode ExitCode = L1ExitCode.RestartLayer1;

static int Main(string[] args)
{
    try
    {
        // These events only work if we have a Windows message pump
        Microsoft.Win32.SystemEvents.SessionEnding += SessionEnding;
        Microsoft.Win32.SystemEvents.SessionEnded += SessionEnded;
        Microsoft.Win32.SystemEvents.SessionSwitch += SessionSwitch;

        RunLayer1();
    }
    finally
    {
        Microsoft.Win32.SystemEvents.SessionEnding -= SessionEnding;
        Microsoft.Win32.SystemEvents.SessionEnded -= SessionEnded;
        Microsoft.Win32.SystemEvents.SessionSwitch -= SessionSwitch;
    }
    return (int)ExitCode;
}

static void SessionEnding(object sender, Microsoft.Win32.SessionEndingEventArgs args) => sessionEvent(SessionEvent.SessionEnding);
static void SessionEnded(object sender, Microsoft.Win32.SessionEndedEventArgs args) => sessionEvent(SessionEvent.SessionEnded);
static void SessionSwitch(object sender, Microsoft.Win32.SessionSwitchEventArgs args) => sessionEvent(SessionEvent.SessionSwitch);

static void sessionEvent(SessionEvent sessionEvent)
{
    switch(sessionEvent)
    {
        case SessionEvent.SessionEnded:
        case SessionEvent.SessionEnding:
            ExitCode = L1ExitCode.UserLoggingOut;
            // Trigger layer1 shutdown
            break;
        case SessionEvent.SessionSwitch:
        default:
            // Do nothing
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户注销时，首先出现`SessionEvent.SessionEnding`，紧接着出现`SessionEvent.SessionEnded`。

`Microsoft.Win32.SystemEvents` 的[文档声明我们感兴趣的事件只有在消息泵运行时才会被引发。恰好第 1 层必须有一个消息循环。](https://docs.microsoft.com/en-us/dotnet/api/microsoft.win32.systemevents)

## 消息循环

我们平台的一个奇怪之处是 [EC](https://en.wikipedia.org/wiki/Embedded_controller) 驱动程序将电源按钮按下作为低级键盘事件。

安装钩子并开始消息循环:

```
private delegate IntPtr LowLevelKeyboardProc(int nCode, IntPtr wp, IntPtr lp);
LowLevelKeyboardProc lpfn;
IntPtr hHook;

bool setHWInterface2()
{
    cts = new CancellationTokenSource();
    keyboardHookProcTask = Task.Factory.StartNew((_) =>
    {
        // Need to create delegate that won't get GC'd
        lpfn = new LowLevelKeyboardProc(KeyboardHookProc);

        var moduleHandle = GetModuleHandle(null);
        //using (var curModule = System.Diagnostics.Process.GetCurrentProcess().MainModule)
        //{
        // var moduleHandle = GetModuleHandle(curModule.ModuleName);
        // // SetWindowsHookEx()
        //}
        hHook = SetWindowsHookEx(WH_KEYBOARD_LL, lpfn, moduleHandle, 0);
        if (hHook == IntPtr.Zero)
        {
            Log("SetWindowsHookEx failed", LogLevel.Error);
        }
        else
        {
            // SetWindowsHookEx requires a Windows message loop on the thread

            winMsgLoopAppCtx = new ApplicationContext();
            Application.Run(winMsgLoopAppCtx);
            // Using Application.DoEvents() seems cleaner, but hook function wasn't getting called
            //while (!cts.IsCancellationRequested)
            //{
            // Application.DoEvents();
            // // Sleep long enough this thread rarely runs, but short enough the button will be responsive
            // await Task.Delay(250, cts.Token);
            //}

            if (!UnhookWindowsHookEx(hHook))
                Log("UnhookWindowsHookEx failed", LogLevel.Warn);
        }
    }, cts.Token, TaskCreationOptions.LongRunning);
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

保留对委托的引用很有趣，因为如果没有它，你可能会以运行时异常结束: [`A callback was made on a garbage collected delegate`](https://stackoverflow.com/questions/6193711/call-has-been-made-on-garbage-collected-delegate-in-c) 。

当`WH_KEYBOARD_LL`事件发生时，使用 [SetWindowsHookEx](https://docs.microsoft.com/en-us/windows/desktop/api/winuser/nf-winuser-setwindowshookexw) 注册一个回调。

我们创建一个[应用程序上下文](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.applicationcontext)并将其传递给[应用程序。运行()](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.application.run#System_Windows_Forms_Application_Run_System_Windows_Forms_ApplicationContext_)来创建我们的消息循环。到后来停止它:

```
winMsgLoopAppCtx.ExitThread(); 
```

Enter fullscreen mode Exit fullscreen mode

也有可能使用 [`Application.DoEvents()`](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.application.doevents?redirectedfrom=MSDN&view=netframework-4.7.2#System_Windows_Forms_Application_DoEvents) ，但是我们没能成功。

我们的挂钩回调是基于[pinvoke.net 样本](https://www.pinvoke.net/default.aspx/Structures/KBDLLHOOKSTRUCT.html) :

```
IntPtr KeyboardHookProc(int code, IntPtr wParam, IntPtr lParam)
{
    if (code >= 0)
    {
        var kbScan = (KBDLLHOOKSTRUCT)Marshal.PtrToStructure(lParam, typeof(KBDLLHOOKSTRUCT));

        // Filter spurious power button events, play audio feedback via PC speaker (also in EC driver), etc.
    }

    // Ensure other applications that have installed hooks receive hook notifications
    return CallNextHookEx(hHook, code, wParam, lParam);
} 
```

Enter fullscreen mode Exit fullscreen mode

在 [LowLevelKeyboardProc](https://msdn.microsoft.com/en-us/library/ms644985%28v=VS.85%29.aspx?f=255&MSPPError=-2147217396) 中有更多的细节。

## 红色大按钮

在开发过程中，第 0 层和第 1 层通常都作为控制台应用程序运行。因为 [layer0 会重启 layer1](//./more-windows-service-in-c-3keg) ，我们原本要先停 layer0 再停 layer1。这是相当恼人的，所以我们改变了它，如果两个窗口都关闭:

```
static OS.Pinvoke.ConsoleCtrlDelegate ConsoleCtrlDelegate;

public static void StartLayerN(ILayerN layer)
{
    // If user hits ctrl-c or closes console window, shut everything down
    ConsoleCtrlDelegate += ctrlTypes =>
    {
        switch (ctrlTypes)
        {
            case OS.Pinvoke.CtrlTypes.CTRL_C_EVENT:
            case OS.Pinvoke.CtrlTypes.CTRL_CLOSE_EVENT:
                // Tell layer0 and layer1 to exit

                // Need to wait here otherwise OS terminates the process (without waiting for layer1, etc.)
                layer.Wait();
                // We handled the signal and thus return true as per https://docs.microsoft.com/en-us/windows/console/handlerroutine
                return true;
        }
        return false;
    };
    OS.Pinvoke.SetConsoleCtrlHandler(ConsoleCtrlDelegate, true);

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

这个项目给了我们很多机会去探索错综复杂的 Windows APIs。我不能说这一切都是令人愉快的，但肯定是有教育意义的。

将来我可能会再发一些帖子——你可以在 Windows 上做这么多有趣(和复杂)的事情。但是，我想我暂时已经做得够多了。