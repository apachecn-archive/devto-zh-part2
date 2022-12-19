# 在外部终端中运行命令。网络核心

> 原文：<https://dev.to/equiman/run-a-command-in-external-terminal-with-net-core-d4l>

。Net Core 是一个强大的工具，但有一些限制，其中之一是 macOS 上的 process startinfoworking directory 不起作用，而在 Windows 上创建一个新的终端需要凭证。

但是您可以转危为安，使用 ProcessStartInfo 的优秀部分作为桥梁，调用能够完成这项工作的外部脚本(即使没有凭证)。

# 简单易行的方法(推荐)

我已经创建了工具箱库，包括这个功能。

[dein ToolBox-C # NETCore Library，包含命令行、文件、日志、平台、系统、转换和验证等实用程序【适用于 Win+Mac】](https://github.com/deinsoftware/toolbox)

只需使用以下命令将其添加到您的项目中:

```
dotnet add package dein.ToolBox
dotnet add package Newtonsoft.Json --version 11.0.2 
```

Enter fullscreen mode Exit fullscreen mode

实现桥接系统和外壳配置器:

<figure>

```
using ToolBox.Bridge;

class Program
{
    public static IBridgeSystem _bridgeSystem { get; set; }
    public static ShellConfigurator _shell { get; set; }

    static void Main(string[] args)
    {
        switch (OS.GetCurrent())
        {
            case "win":
                _bridgeSystem = BridgeSystem.Bat;
                break;
            case "mac":
            case "gnu":
                _bridgeSystem = BridgeSystem.Bash;
                break;
        }
        _shell = new ShellConfigurator(_bridgeSystem);
        //Foo()
        //Bar()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-bridge-factory.cs</figcaption>

</figure>

通过[默认使用一个 notification system](https://github.com/deinsoftware/toolbox/blob/master/ToolBox/Notification/NotificationSystemDefault.cs)，但是你可以通过实现`INotificationSystem`接口定制它，并在`ShellConfigurator`上作为一个参数发送它。

<figure>

```
using static ToolBox.Notification;

class Program
{
    public static INotificationSystem _notificationSystem { get; set; }
    public static IBridgeSystem _bridgeSystem { get; set; }
    public static ShellConfigurator _shell { get; set; }

    static void Main(string[] args)
    {
        _notificationSystem = new MyConsoleNotificationSystem();
        switch (OS.GetCurrent())
        {
            case "win":
                _bridgeSystem = BridgeSystem.Bat;
                break;
            case "mac":
            case "gnu":
                _bridgeSystem = BridgeSystem.Bash;
                break;
        }
        _shell = new ShellConfigurator(_bridgeSystem, _notificationSystem);
        //Foo()
        //Bar()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-bridge-notification.cs</figcaption>

</figure>

要理解这个库是如何工作的，请看一下示例文件夹内部。使用指南比文字更容易。

# 艰难道

在根项目文件夹中，创建两个可以接收命令和目录(可选)的脚本(Batch 和 Bash)。在内部，他们将在新窗口中创建外部终端:

用于 Windows 的 Bat 文件:

<figure>

```
echo off
set cmd=%1
set dir=%2
if defined dir (
 cd /d %dir%\
)
start cmd.exe /K %cmd%
cls
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>cmd.bat</figcaption>

</figure>

macOS 的 Bash 文件:

<figure>

```
#!/bin/bash
cmd=”$1";
dir=”$2";
if [ -n “$dir” ]; then osascript <<EOF tell application “Terminal” to do script “cd $dir; $cmd” EOF else osascript <<EOF tell application “Terminal” to do script “$cmd” EOF fi clear
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>cmd.sh</figcaption>

</figure>

记得给这个文件分配执行权限:`chmod +x cmd.mac.sh`

补充一个更好的。使用此类检测网络核心操作系统:

<figure>

```
using System;
using System.Runtime.InteropServices;

namespace ToolBox.Platform
{
    public static class OS
    {
        public static bool IsWin() =>
            RuntimeInformation.IsOSPlatform(OSPlatform.Windows);

        public static bool IsMac() =>
            RuntimeInformation.IsOSPlatform(OSPlatform.OSX);

        public static bool IsGnu() =>
            RuntimeInformation.IsOSPlatform(OSPlatform.Linux);

        public static string GetCurrent()
        {
            return
            (IsWin() ? "win" : null) ??
            (IsMac() ? "mac" : null) ??
            (IsGnu() ? "gnu" : null);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Platform.cs</figcaption>

</figure>

使用 **ProcessStartInfo** 但是理解他的局限性。该类可以运行隐藏命令(在后台运行命令)、内部命令(在同一终端运行命令并显示`stdout`和`stderr`)和外部命令(在外部终端运行命令)。

<figure>

```
using System.Runtime.InteropServices;
using System;
using System.Diagnostics;
using System.Text;

using System.Reflection;
using System.IO;

namespace ToolBox.Bridge
{
    public class Response {
        public int code { get; set; }
        public string stdout { get; set; }
        public string stderr { get; set; }
    }

    public enum Output {
        Hidden,
        Internal,
        External
    }

    public static class Shell
    {
        private static string GetFileName()
        {
            string fileName = "";
            try
            {
                switch (OS.WhatIs())
                {
                    case "win":
                        fileName = "cmd.exe";
                        break;
                    case "mac":
                    case "gnu":
                        fileName = "/bin/bash";
                        break;
                }
            }
            catch (Exception Ex)
            {
                Console.WriteLine(Ex.Message);
            }
        }

        private static string CommandConstructor (string cmd, Output? output = Output.Hidden, string dir = "")
        {
            try
            {
                if (!String.IsNullOrEmpty(dir))
                {
                    dir.Exists("");
                }
                switch (OS.WhatIs())
                {
                    case "win":
                        if (!String.IsNullOrEmpty(dir))
                        {
                            dir = $" \"{dir}\"";
                        }
                        if (output == Output.External)
                        {
                            cmd = $"{Directory.GetCurrentDirectory()}/cmd.win.bat \"{cmd}\"{dir}";
                        }
                        cmd = $"/c \"{cmd}\"";
                        break;
                    case "mac":
                    case "gnu":
                        if (!String.IsNullOrEmpty(dir))
                        {
                            dir = $" '{dir}'";
                        }
                        if (output == Output.External)
                        {
                            cmd = $"sh {Directory.GetCurrentDirectory()}/cmd.mac.sh '{cmd}'{dir}";
                        }
                        cmd = $"-c \"{cmd}\"";
                        break;
                }
                return cmd;
            }
            catch (Exception Ex)
            {
                Console.WriteLine(Ex.Message);
            }
        }

        public static Response Term (string cmd, Output? output = Output.Hidden, string dir = ""){
            var result = new Response();
            var stderr = new StringBuilder();
            var stdout = new StringBuilder();
            try
            {
                ProcessStartInfo startInfo = new ProcessStartInfo();
                startInfo.FileName = GetFileName();
                startInfo.Arguments = CommandConstructor(cmd, output, dir);
                startInfo.RedirectStandardOutput = !(output == Output.External);
                startInfo.RedirectStandardError = !(output == Output.External);
                startInfo.UseShellExecute = false;
                startInfo.CreateNoWindow = !(output == Output.External);
                if (!String.IsNullOrEmpty(dir) && output != Output.External){
                    startInfo.WorkingDirectory = dir;
                }

                using (Process process = Process.Start(startInfo))
                {
                    switch (output)
                    {
                        case Output.Internal:
                            $"".fmNewLine();

                            while (!process.StandardOutput.EndOfStream) {
                                string line = process.StandardOutput.ReadLine();
                                stdout.AppendLine(line);
                                Console.WriteLine(line);
                            }

                            while (!process.StandardError.EndOfStream) {
                                string line = process.StandardError.ReadLine();
                                stderr.AppendLine(line);
                                Console.WriteLine(line);
                            }
                            break;
                        case Output.Hidden:
                            stdout.AppendLine(process.StandardOutput.ReadToEnd());
                            stderr.AppendLine(process.StandardError.ReadToEnd());
                            break;
                    }
                    process.WaitForExit();
                    result.stdout = stdout.ToString();
                    result.stderr = stderr.ToString();
                    result.code = process.ExitCode;
                }
            }
            catch (Exception Ex)
            {
                Console.WriteLine(Ex.Message);
            }
            return result;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Platform.cs</figcaption>

</figure>

记住在`.csproj`文件上添加这个标签，以便在发布项目时复制脚本文件:

<figure>

```
<ItemGroup Condition="'$(TargetFramework)' == 'netcoreapp2.0'">
    <None Update="cmd.sh" CopyToOutputDirectory="PreserveNewest" />
    <None Update="cmd.bat" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>shell.csproj</figcaption>

</figure>

# 用法(魔法成真)

术语函数总是返回一个带有代码、`stdout`和`stderr`的响应类。

<figure>

```
namespace ToolBox.Bridge 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-namespace.cs</figcaption>

</figure>

## 隐藏

您可以静默运行命令。不要在控制台上显示任何内容，如果您想打印结果，请在响应返回时使用值。

<figure>

```
Response result = Term("dotnet --version", Output.Hidden);
Result(result.stdout, "Not Installed");
Console.WriteLine(result.code.ToString());
if (result.code == 0)
{
    Console.WriteLine($"Command Works :D");
}
else
{
    Console.WriteLine(result.stderr);
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-hidden-example.cs</figcaption>

</figure>

## 内部

您可以在同一终端上运行命令并查看结果。

<figure>

```
Term("java -version 2>&1", Output.Internal); 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-internal-example.cs</figcaption>

</figure>

## 外部

**大口浏览器 Sync** 需要保持打开，以便让“web 服务器”运行。这是**在外部窗口运行它的主要原因**。

Gulp 项目路径在 Windows 和 macOS 之间是不同的，那么我认为更好的做法是创建一个名为`GULP_PROJECT`的环境变量。

<figure>

```
Term($”gulp browserSync”, Output.External, Environment.GetEnvironmentVariable(“GULP_PROJECT”)); 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>toolbox-external-example.cs</figcaption>

</figure>

另一个用途是，如果你需要同时运行一个进程，在一个新的终端中打开每一个，不需要等到完成一个再运行下一个。

# 结论

为什么越简单越好？

因为不需要处理文件、脚本权限、项目构建配置；NuGet 包为你做这一切。此外，您可以创建另一个实现`IBridge`(如 Zsh、PowerShell 等)接口的 shell，并在 ShellConfigurator 上将其作为参数使用……工具箱库附带了一些实用程序，如:命令行、文件、日志、平台、系统、转换和验证。

Hard way 适合作为研究案例，或者如果您希望更好地了解它是如何工作的，但是您将手动处理文件权限，并且相信您不希望最终用户运行额外的命令。最后……没有使用多态性开发，那么你将不得不手动处理新的添加。

# 奖金追踪

看看这个库的内部，它不仅可以启动外部命令:

*   [dein ToolBox-C # NETCore Library，包含命令行、文件、日志、平台、系统、转换和验证等实用程序【适用于 Win+Mac】](https://www.nuget.org/packages/dein.ToolBox/)

* * *

**都是乡亲们！**
**快乐编码** 🖖

[![beer](../Images/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)