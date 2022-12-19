# C#中的更多 Windows 服务

> 原文：<https://dev.to/jeikabu/more-windows-service-in-c-3keg>

先前，我讨论了一个我们称之为“层 0”的 Windows 服务。

我们的应用程序还有一个额外的问题，即该服务需要与用户及其桌面进行交互。[互动服务](https://docs.microsoft.com/en-us/windows/desktop/Services/interactive-services)提供了如何实现这一点的指导。基本上，作为用户生成一个桌面应用程序，并使用 [IPC](https://en.wikipedia.org/wiki/Inter-process_communication) 在两者之间进行通信。我们将这部分客户称为“第 1 层”。

## 会话事件

为了让第 0 层服务作为桌面用户生成第 1 层进程，我们需要跟踪用户登录/注销活动。我们将修改从 ServiceBase 派生的[类。](//./windows-service-in-c-1m79#the-service)

首先，启用 [ServiceBase。CanHandleSessionChangeEvent](https://docs.microsoft.com/en-us/dotnet/api/system.serviceprocess.servicebase.canhandlesessionchangeevent):

```
public Layer0Service()
{
    CanHandleSessionChangeEvent = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后覆盖 [OnSessionChange](https://docs.microsoft.com/en-us/dotnet/api/system.serviceprocess.servicebase.onsessionchange) :

```
protected override void OnSessionChange(SessionChangeDescription changeDescription)
{
    Log("OnSessionChange: " + changeDescription.Reason);
    base.OnSessionChange(changeDescription);
    switch (changeDescription.Reason)
    {
        case SessionChangeReason.SessionLogon:
        case SessionChangeReason.SessionUnlock: // Switch between logged in users.
            DoLogon();
            break;
        case SessionChangeReason.SessionLogoff:
        //case SessionChangeReason.SessionLock:
            DoLogoff();
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Logon`和`Logoff`事件是不言自明的。`Unlock`(以及相应的`Lock`)更是如此。例如，当使用快速用户切换时，会发生解锁事件。

## 以桌面用户身份启动流程

编写 layer0 服务作为当前桌面用户如何创建进程:

```
#if DEBUG // Show console window
const Pinvoke.CreationFlags Layer1CreationFlags = Pinvoke.CreationFlags.CreateNewConsole;
#else const Pinvoke.CreationFlags Layer1CreationFlags = Pinvoke.CreationFlags.CreateNoWindow;
#endif 
Pinvoke.PROCESS_INFORMATION? serviceStartLayer1AsUser(string exePath, string command)
{
    IntPtr server = IntPtr.Zero;
    IntPtr ppSessionInfo = IntPtr.Zero;
    IntPtr userToken = IntPtr.Zero; // WARNING: the user token is supposed to be a secret, don't print it anywhere
    try
    {
        // Query all sessions on local machine
        server = Pinvoke.WTSOpenServer("localhost");
        Int32 count = 0;
        Int32 retval = Pinvoke.WTSEnumerateSessions(server, ref ppSessionInfo, ref count);
        if (retval == 0)
        {
            throw new Win32Exception(Marshal.GetLastWin32Error(), "WTSEnumerateSessions");
        }

        // Find session for the logged in user and get their token
        Int32 dataSize = Marshal.SizeOf(typeof(Pinvoke.WTS_SESSION_INFO));
        Int64 current = (Int64)ppSessionInfo;
        for (int i = 0; i < count; i++)
        {
            var si = (Pinvoke.WTS_SESSION_INFO)Marshal.PtrToStructure((IntPtr)current, typeof(Pinvoke.WTS_SESSION_INFO));
            current += dataSize;

            if (si.State != Pinvoke.WTS_CONNECTSTATE_CLASS.WTSActive && si.State != Pinvoke.WTS_CONNECTSTATE_CLASS.WTSConnected)
                continue;

            var sessionId = (uint)si.SessionID;
            // WARNING: the user token is supposed to be a secret, don't print it anywhere
            if (OS.Pinvoke.WTSQueryUserToken(sessionId, out userToken))
            {
                Log(LogLevel.Info, "WTSQueryUserToken succeeded for session: " + sessionId);
                break;
            }
        }

        if (userToken == IntPtr.Zero)
        {
            Log(LogLevel.Error, "Unable to obtain user token, unable to start layer1");
            return null;
        }

        // Launch layer1 as the logged-in user
        var nullSecurityAttributes = new Pinvoke.SECURITY_ATTRIBUTES { lpSecurityDescriptor = IntPtr.Zero };
        Pinvoke.STARTUPINFO startupInfo = Pinvoke.StartupInfoAlloc();
        Pinvoke.PROCESS_INFORMATION processInfo;
        // WARNING: the user token is supposed to be a secret, don't print it anywhere
        Pinvoke.CreateProcessAsUser(userToken, exePath, command,
            ref nullSecurityAttributes, ref nullSecurityAttributes,
            true, Layer1CreationFlags, IntPtr.Zero, null, ref startupInfo, out processInfo);

        return processInfo;
    }
    finally
    {
        if (server != IntPtr.Zero)
            Pinvoke.WTSCloseServer(server);
        if (ppSessionInfo != IntPtr.Zero)
            Pinvoke.WTSFreeMemory(ppSessionInfo);
        if (userToken != IntPtr.Zero)
            Pinvoke.CloseHandle(userToken);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

亮点:

1.  使用 [WTSEnumerateSessions](https://docs.microsoft.com/en-us/windows/desktop/api/wtsapi32/nf-wtsapi32-wtsenumeratesessionsa) 遍历所有会话，查找活动桌面会话。
2.  WTSQueryUserToken 获取该会话用户的主访问令牌。
3.  [CreateProcessAsUser](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682429%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396) 以该用户身份启动一个流程(即层 1)。

现在第 0 层服务和第 1 层进程都在运行。

评论:

*   我应该注明这段代码的来源，但我没有记下来。有很多谷歌搜索结果非常相似:
    *   [https://blogs . msdn . Microsoft . com/winsdk/2009/07/14/launching-an-interactive-process-from-windows-service-in-windows-vista-and-later/](https://blogs.msdn.microsoft.com/winsdk/2009/07/14/launching-an-interactive-process-from-windows-service-in-windows-vista-and-later/)
    *   [https://www . code project . com/Articles/36581/Interaction-between-services-and-applications-at-u](https://www.codeproject.com/Articles/36581/Interaction-between-services-and-applications-at-u)
*   也许可以用[wtsgactiveconsolesessionid()](https://docs.microsoft.com/en-us/windows/desktop/api/winbase/nf-winbase-wtsgetactiveconsolesessionid)替换所有会话上的循环

## 控制台可执行

快闪开。

[我提到过](//./windows-service-in-c-1m79)我们希望我们的开发者能够将 layer0 作为一个控制台应用来启动。当层 0 是桌面应用而不是服务时，启动层 1 更简单:

```
Pinvoke.PROCESS_INFORMATION? startLayer1(string exePath, string commandline)
{
    var nullSecurityAttributes = new Pinvoke.SECURITY_ATTRIBUTES { lpSecurityDescriptor = IntPtr.Zero };
    Pinvoke.STARTUPINFO startupInfo = Pinvoke.StartupInfoAlloc();
    Pinvoke.PROCESS_INFORMATION procInfo;
    Pinvoke.CreateProcess(exePath, commandline,
        ref nullSecurityAttributes, ref nullSecurityAttributes,
        true, Layer1CreationFlags, IntPtr.Zero, null,
        ref startupInfo, out procInfo);
    return procInfo;
} 
```

Enter fullscreen mode Exit fullscreen mode

启动另一个 exe 的规范解决方案是 [`System.Diagnostics.Process`](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.process) 。然而，通过使用 [CreateProcess](https://docs.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-createprocessa) (它也返回 [PROCESS_INFORMATION](https://docs.microsoft.com/en-us/windows/desktop/api/processthreadsapi/ns-processthreadsapi-_process_information) )，这意味着无论是从服务还是控制台应用程序启动，管理第 1 层都是一样的。

## 过程扯皮

一旦第 1 层进程运行，我们就有了许多特定于我们的应用程序的行为和处理。最有趣的是第 0 层对第 1 层的监控:

```
var objState = (ObjectState)Pinvoke.WaitForSingleObject(layer1ProcInfo.hProcess, 0);
if (user_logout)
{
    // There's no longer a user
    if (objState == Pinvoke.ObjectState.WaitObject0)
    {
        // Already not running. Wait for a user to login.
    }
    else if (objState == Pinvoke.ObjectState.WaitTimeout)
    {
        // Running. Tell it to stop.
    }
}
else if (user_login_or_fast_user_switching)
{
    // There's a user (may or may not have been one before)
    if(objState == Pinvoke.ObjectState.WaitObject0)
    {
        // Not running, so start it.
    }
    else if(objState == Pinvoke.ObjectState.WaitTimeout)
    {
        // Running as different user. Stop it, then restart it as new user
    }
}
else
{
    // Nothing special has happened. This is the state we're normally in.
    if (objState == Pinvoke.ObjectState.WaitObject0)
    {
        // Process not running. Check the exit code to see if it was intentional.
        if (Pinvoke.GetExitCodeProcess(procInfo.hProcess, out uint lpExitCode) && lpExitCode == (uint)L1ExitCode.UserLoggingOut)
        {
            // Process exited but it was told to because user is logging out. We're probably going to receive a "user logout" event.
        }
        else
        {
            // Process stopped/crashed. Restart it.
        }
    }
    else if (objState == Pinvoke.ObjectState.WaitTimeout)
    {
        // Still running. Everything ok- do nothing.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想要的行为:

*   等待用户登录以启动第 1 层
*   如果第一层崩溃，重启它
*   如果切换用户，需要停止层 1，并作为新用户重新启动它
*   用户注销期间停止层 1

这里我们用 [`WaitForSingleObject()`](https://docs.microsoft.com/en-us/windows/desktop/api/synchapi/nf-synchapi-waitforsingleobject) 来监控 layer1 进程。第一个参数是进程句柄，第二个参数是`0`,因此函数立即返回进程的当前状态:

*   `WaitTimeout`表示它正在运行
*   `WaitObject0`表示不是

[`GetExitCodeProcess()`](https://docs.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-getexitcodeprocess) 是最近才加入的。查看日志输出，我们注意到在用户发起注销后，第 1 层进程退出，然后第 0 层尝试重新启动它失败；它要么无法启动，要么立即退出。层 0 一直尝试重启层 1，直到调用`OnSessionChange(SessionLogoff)`(层 1 首次退出后几秒钟)。我们使用退出代码通知第 0 层，该进程打算停止，不应该重新启动。当我讨论第 1 层的细节时，将会涉及其中的一部分。

## 走弯路

[互动服务](https://docs.microsoft.com/en-us/windows/desktop/Services/interactive-services)提到了将`SERVICE_INTERACTIVE_PROCESS`传递给`CreateService()`的替代方案。我们没有采用这种方法有两个原因:

1.  `NoInteractiveServices`从 Windows 8 开始默认设置注册表项；`SERVICE_INTERACTIVE_PROCESS`即将被弃用。
2.  我们的应用程序需要启动第三方应用程序，从服务启动的流程有一个不寻常的执行环境。兼容性问题是一个问题。

由于[我们对](//./migrating-to-aspnet-core-w-apache-thrift-45f6) [Apache Thrift](https://thrift.apache.org/) 的使用，我们在第 1 层和第 0 层(服务)之间有了完整的 IPC 消息传递解决方案。层 0 在启动层 1 之前开始监听，层 1 一启动就连接。