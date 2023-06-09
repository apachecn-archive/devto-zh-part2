# 在 C#中加载本机库

> 原文：<https://dev.to/jeikabu/loading-native-libraries-in-c-fh6>

虽然 C#可以调用本地库中的函数，但您必须避免在运行时混合使用 32 位和 64 位二进制文件，以免招致`BadImageFormatException`的愤怒。你有祸了。

不同的硬件和操作系统有不同的注意事项。这主要是指 64 位的 Windows 10，偶尔也包括 OSX (64 位)。

## 【任意 CPU】

。Net 程序集可以用“任何 CPU”的平台目标进行编译。[这个 SO 回答](https://stackoverflow.com/questions/5229768/c-sharp-compiling-for-32-64-bit-or-for-any-cpu)很好地涵盖了它:

*   以“任何 CPU”为目标的二进制将 [JIT](https://en.wikipedia.org/wiki/Just-in-time_compilation) 到“任何”架构(x86、x64、ARM 等)。)——但一次只能一个。
*   在 64 位 Windows 10 上，“任意 CPU”二进制文件将 JIT 到 x64，并且它只能加载 x64 原生 dll。

如果您尝试将 32 位程序集加载到 64 位进程中会发生什么情况(反之亦然)？ [`BadImageFormatException`](https://docs.microsoft.com/en-us/dotnet/api/system.badimageformatexception?view=netframework-4.7.2) 。

## Windows“黑掉”

Pinvoke 是一种从 C#中调用原生 dll 函数的方法。

几年来，我一直使用一个众所周知的技巧，在 Windows 桌面应用程序中选择性地加载 32/64 位的本地库:

```
class ADLWrapper
{
    [DllImport("LibADLs")]
    static extern int LibADLs_GetAdapterIndex(IntPtr ptr);

    static ADLWrapper()
    {
        // If 64-bit process, need to load 64-bit native dll. Otherwise, 32-bit dll.
        // Both dlls need to have same filename and be dllexport'ing the same functions.
        if (System.Environment.Is64BitProcess)
        {
            var handle = LoadLibraryEx(pathTo64bitLibs + "LibADLs.dll", IntPtr.Zero, 0);
            if (handle != IntPtr.Zero)
            {
                //...
            }
        }
        else
        {
            // Load 32-bit dll
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这假设您有两个名称相同、路径不同的共享库。比如`x86/libadls.dll`和`x64/libadls.dll`。这是对动态库被发现的方式[的轻微滥用](https://docs.microsoft.com/en-us/windows/desktop/dlls/dynamic-link-library-search-order)。在这种情况下，围绕 AMD 的显示库(ADL)的支持库的 C#包装器的片段。

在推出 [Is64BitProcess](https://docs.microsoft.com/en-us/dotnet/api/system.environment.is64bitprocess?view=netframework-4.7.2) 之前。Net 4.0，可以使用`(IntPtr.Size == 8)`的值来代替。

当处理不同名称的动态库时(ADL 就是这种情况)，因为 [`DllImportAttribute`](https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.dllimportattribute(v=vs.110).aspx) 的参数必须是一个常量，所以我们有

```
interface ILibADL
{
    Int64 GetSerialNumber(int adapter);
}

class LibADL64 : ILibADL
{
    [DllImport("atiadlxx.dll")] // 64-bit dll
    static extern int ADL_Adapter_SerialNumber_Get(int adapter, out Int64 serial);

    public Int64 GetSerialNumber(int adapter)
    {
        Int64 serial;
        if (ADL_Adapter_SerialNumber_Get(adapter, out serial) == 0)
        {
            return serial;
        }
        return -1;
    }
}

class LibADL32 : ILibADL
{
    [DllImport("atiadlxy.dll")] // 32-bit dll
    static extern int ADL_Adapter_SerialNumber_Get(int adapter, out Int64 serial);

    //... 
```

Enter fullscreen mode Exit fullscreen mode

然后在别的地方:

```
if (Environment.Is64BitProcess)
{
    adl = new LibADL64();
}
else
{
    adl = new LibADL32();
} 
```

Enter fullscreen mode Exit fullscreen mode

简单有效。但是不能在 OSX(或者 Linux)上运行。

## GetDelegateForFunctionPointer

[我第一次遇到的另一种方法是](https://github.com/mhowlett/NNanomsg/blob/master/NNanomsg/Interop.cs#L193)在试验[时，NNanomsg](https://github.com/zplus/NNanomsg) 使用[GetDelegateForFunctionPointer()](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.marshal.getdelegateforfunctionpointer):

```
[UnmanagedFunctionPointer(CallingConvention.Cdecl)]
public delegate int nn_socket_delegate(int domain, int protocol);
public static nn_socket_delegate nn_socket;

static void InitializeDelegates(IntPtr nanomsgLibAddr, NanomsgLibraryLoader.SymbolLookupDelegate lookup)
{
    nn_socket = (nn_socket_delegate)Marshal.GetDelegateForFunctionPointer(lookup(nanomsgLibAddr, "nn_socket"), typeof(nn_socket_delegate));
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`lookup`在 Windows 上是 [`GetProcAddress()`](https://msdn.microsoft.com/en-us/library/windows/desktop/ms683212(v=vs.85).aspx) (在 posix 平台上是`dlsym()`)。

gRPC 使用了类似的方法[。](https://github.com/grpc/grpc/blob/master/src/csharp/Grpc.Core/Internal/UnmanagedLibrary.cs#L114)

好的，但是对于像 nng 这样的大型 API 来说就有些笨拙了(这也是我们前进的方向)。

## 游泳等。

很难谈论与本机代码的接口而不碰到 [SWIG](http://swig.org/) (或其他类似的技术)。

在过去的 15 年里，我们见过几次面。第一次是负责为 Linux 虚拟设备驱动程序创建[python 1.5 包装器。最近，](http://www.ittc.ku.edu/kurt/)[半心半意地尝试与 AllJoyn](///2016/03/02/c-wrapper-for-alljoyn-via-swig.html) 一起使用。

但是它总是以同样的方式结束:试图调试我(错误地)使用一个重要工具的挫败感，以及对由此产生的机器人代码的困惑。

## 【现代】Nupkg

需求是发明之母，[支持多种平台](https://blog.3d-logic.com/2015/11/10/using-native-libraries-in-asp-net-5/)引发了[nu get 包装的进步](///2018/08/15/nupkg-with-native.html)来解决这个问题。

我们一直在尝试获得一个包含本地库( [nng](https://github.com/nanomsg/nng) )的 nupkg 来处理 OSX:

1.  构建动态库(libnng.dylib): `mkdir build && cd build && cmake -G Ninja -DBUILD_SHARED_LIBS=ON .. && ninja`
2.  复制到 nupkg 的`runtimes/osx-x64/native`

在 [RepSocket.cs](https://github.com/zplus/csnng/blob/master/src/Nanomsg2.Sharp/Protocols/Reqrep/RepSocket.cs#L23) 中:

```
[DllImport("nng", EntryPoint = "nng_rep0_open", CallingConvention = Cdecl)]
[return: MarshalAs(I4)]
private static extern int __Open(ref uint sid); 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 上运行良好，但在 OSX 上:

```
Mono: DllImport error loading library 'libnng': 'dlopen(libnng, 9): image not found'.
Mono: DllImport error loading library 'libnng.dylib': 'dlopen(libnng.dylib, 9): image not found'.
Mono: DllImport error loading library 'libnng.so': 'dlopen(libnng.so, 9): image not found'.
Mono: DllImport error loading library 'libnng.bundle': 'dlopen(libnng.bundle, 9): image not found'.
Mono: DllImport error loading library 'nng': 'dlopen(nng, 9): image not found'.
Mono: DllImport error loading library '/Users/jake/test/bin/Debug/libnng': 'dlopen(/Users/jake/test/bin/Debug/libnng, 9): image not found'.
Mono: DllImport error loading library '/Users/jake/test/bin/Debug/libnng.dylib': 'dlopen(/Users/jake/test/bin/Debug/libnng.dylib, 9): image not found'.
Mono: DllImport error loading library '/Users/jake/test/bin/Debug/libnng.so': 'dlopen(/Users/jake/test/bin/Debug/libnng.so, 9): image not found'.
...
Mono: DllImport error loading library '/Library/Frameworks/Mono.framework/Versions/5.12.0/lib/libnng.dylib': 'dlopen(/Library/Frameworks/Mono.framework/Versions/5.12.0/lib/libnng.dylib, 9): image not found'.
... 
```

Enter fullscreen mode Exit fullscreen mode

通过设置环境变量
启用附加库加载信息

```
# .NET Framework
MONO_LOG_MASK=dll
MONO_LOG_LEVEL=info
# .NET Core
DYLD_PRINT_LIBRARIES=YES 
```

Enter fullscreen mode Exit fullscreen mode

*   在 Visual Studio for Mac 中:右键单击项目**选项- >运行。“环境变量”下的默认**
*   Visual Studio 代码:在`.vscode/launch.json`的`"configurations"`部分添加`"env": { "DYLD_PRINT_LIBRARIES":"YES" }`

一个解决方案来自于[使用 ASP.NET 的本地图书馆 5](https://blog.3d-logic.com/2015/11/10/using-native-libraries-in-asp-net-5/) 博客:

1.  预加载 dylib(类似于 Windows)
2.  使用`DllImport("__Internal")`

代码最初基于 [Nnanomsg](https://github.com/mhowlett/NNanomsg) :

```
static LibraryLoader()
{
    // Figure out which OS we're on. Windows or "other".
    if (Environment.OSVersion.Platform == PlatformID.Unix ||
                Environment.OSVersion.Platform == PlatformID.MacOSX ||
                // Legacy mono value. See https://www.mono-project.com/docs/faq/technical/
                (int)Environment.OSVersion.Platform == 128)
    {
        LoadPosixLibrary();
    }
    else
    {
        LoadWindowsLibrary();
    }
}

static void LoadPosixLibrary()
{
    const int RTLD_NOW = 2;
    string rootDirectory = AppDomain.CurrentDomain.BaseDirectory;
    string assemblyDirectory = Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);

    // Environment.OSVersion.Platform returns "Unix" for Unix or OSX, so use RuntimeInformation here
    var isOsx = System.Runtime.InteropServices.RuntimeInformation.IsOSPlatform(OSPlatform.OSX);
    string libFile = isOsx ? "libnng.dylib" : "libnng.so";
    // x86 variants aren't in https://docs.microsoft.com/en-us/dotnet/core/rid-catalog
    string arch = (isOsx ? "osx" : "linux") + "-" + (Environment.Is64BitProcess ? "x64" : "x86");

    // Search a few different locations for our native assembly
    var paths = new[]
        {
            // This is where native libraries in our nupkg should end up
            Path.Combine(rootDirectory, "runtimes", arch, "native", libFile),
            // The build output folder
            Path.Combine(rootDirectory, libFile),
            Path.Combine("/usr/local/lib", libFile),
            Path.Combine("/usr/lib", libFile)
        };

    foreach (var path in paths)
    {
        if (path == null)
        {
            continue;
        }

        if (File.Exists(path))
        {
            var addr = dlopen(path, RTLD_NOW);
            if (addr == IntPtr.Zero)
            {
                // Not using NanosmgException because it depends on nn_errno.
                var error = Marshal.PtrToStringAnsi(dlerror());
                throw new Exception("dlopen failed: " + path + " : " + error);
            }
            NativeLibraryPath = path;
            return;
        }
    }

    throw new Exception("dlopen failed: unable to locate library " + libFile + ". Searched: " + paths.Aggregate((a, b) => a + "; " + b));
}

[DllImport("libdl")]
static extern IntPtr dlopen(String fileName, int flags);

[DllImport("libdl")]
static extern IntPtr dlerror();

[DllImport("libdl")]
static extern IntPtr dlsym(IntPtr handle, String symbol); 
```

Enter fullscreen mode Exit fullscreen mode

[系统的使用。runtime . interop services . runtime information](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.runtimeinformation)来自[这个博客](https://codepedia.info/dotnet-core-to-detect-operating-system-os-platform/)。

我们基于主机 OS (Linux vs OSX，32 vs 64 位)构建一个路径，并将其传递给`dlopen()`以预加载共享库。

注意没有文件扩展名`[DllImport("libdl")]`。这将在 OSX 上加载`libdl.dylib`,在 Linux 上加载`libdl.so`。

将导入的函数更改为:

```
[DllImport("__Internal", EntryPoint = "nng_rep0_open", CallingConvention = Cdecl)]
[return: MarshalAs(I4)]
private static extern int __Open(ref uint sid); 
```

Enter fullscreen mode Exit fullscreen mode

调试输出:

```
Native library: /Users/jake/test/bin/Debug/runtimes/osx-x64/native/libnng.dylib
Mono: DllImport attempting to load: '__Internal'.
Mono: DllImport loaded library '(null)'.
Mono: DllImport searching in: '__Internal' ('(null)').
Mono: Searching for 'nng_rep0_open'.
Mono: Probing 'nng_rep0_open'.
Mono: Found as 'nng_rep0_open'. 
```

Enter fullscreen mode Exit fullscreen mode

起作用，但是要求每一次`DllImport`使用`"__Internal"`还有很多不足之处。有几个选择:

*   使用 [T4 模板](https://docs.microsoft.com/en-us/visualstudio/modeling/code-generation-and-t4-text-templates?view=vs-2017)(或其他脚本)来处理每个平台所需的 pinvoke 导入
*   设置 [DYLD_xxx_PATH](https://www.mono-project.com/docs/advanced/pinvoke/#macos-framework-and-dylib-search-path)
*   [`.config`文件与`<dllmap>`](https://www.mono-project.com/docs/advanced/pinvoke/#library-names) 文件
*   作为构建的一部分，只需将 dylib 复制到输出路径(任意脚本都可以作为 nupkg 的一部分)

同样，这种方法可行，但有以下缺点:

*   过度依赖构建/打包工具，这对于调试和正常工作来说是一个麻烦
*   在 Windows 上，需要将**平台目标**设置为`Any CPU`以外的值
*   `DllImport`的参数必须是编译时常数，并且需要消息传递，以获得在所有平台上工作的“魔力”

## 一个人载入上下文来统治他们所有人

遇到了[“装配装载的最佳实践”](https://docs.microsoft.com/en-us/dotnet/framework/deployment/best-practices-for-assembly-loading)。

开始寻找关于这些“加载上下文”的信息，并找到了一个有趣的文档:

> 自定义 LoadContext 可以重写 AssemblyLoadContext。LoadUnmanagedDll 方法从 LoadContext 实例中截取 PInvokes，以便可以从自定义二进制文件中解析

Ho ho.

也看到了在 ASP.NET 核心工作的人的帖子[。](https://natemcmaster.com/blog/2018/07/25/netcore-plugins/)[他在用`AssemblyLoadContext`争论插件](https://github.com/natemcmaster/DotNetCorePlugins)，但是提到`LoadUnmanagedDll`是[“动态加载非托管二进制文件的唯一好方法”](https://natemcmaster.com/blog/2018/07/25/netcore-plugins/#assemblyloadcontext-the-dark-horse)。

要入门，需要[系统。Runtime.Loader](https://www.nuget.org/packages/System.Runtime.Loader/) 包:`dotnet add nng.NETCore package system.runtime.loader`

首先尝试硬编码路径和文件名:

```
public class ALC : System.Runtime.Loader.AssemblyLoadContext
{
    protected override Assembly Load(AssemblyName assemblyName)
    {
        if (assemblyName.Name == "nng.NETCore")
            return LoadFromAssemblyPath("/Users/jake/nng.NETCore/bin/Debug/netstandard2.0/nng.NETCore.dll");
        // Return null to fallback on default load context
        return null;
    }
    protected override IntPtr LoadUnmanagedDll(string unmanagedDllName)
    {
        // Native nng shared library
        return LoadUnmanagedDllFromPath("/Users/jake/nng/build/libnng.dylib");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[`DllImport` ed 方法必须是`static`](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/extern) 所以不能用 [`Activator.CreateInstance()`](https://docs.microsoft.com/en-us/dotnet/api/system.activator.createinstance?view=netframework-4.7.2) 来轻易得手。或许可以使用[反射](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection)来提取它们，但是那样会很麻烦。

我认为关键是来自[的那个 LoadContext 设计文档](https://github.com/dotnet/coreclr/blob/master/Documentation/design-docs/assemblyloadcontext.md#how-load-is-attempted):

> 如果程序集 A1 触发了程序集 C1 的加载，则在前者的 LoadContext 实例中尝试加载后者

基本上，一旦一个加载上下文加载了一个程序集，后续的相关加载就会经历相同的上下文。因此，我用 pinvoke 方法将一个我用来为我的测试创建对象的工厂移动到程序集中:

```
TestFactory factory;

[Fact]
public async Task PushPull()
{
    var alc = new ALC();
    var assem = alc.LoadFromAssemblyName(new System.Reflection.AssemblyName("nng.NETCore"));
    var type = assem.GetType("nng.Tests.TestFactory");
    factory = (TestFactory)Activator.CreateInstance(type);
    //... 
```

Enter fullscreen mode Exit fullscreen mode

我们不能简单地直接调用`static` pinvoke 方法，但是我们可以使用一个定制的加载上下文来实例化一个类型，然后这个类型调用 pinvoke。

我很少发现例外令人兴奋，但这一个是:

```
Exception thrown: 'System.InvalidCastException' in tests.dll: 
'[A]nng.Tests.TestFactory cannot be cast to [B]nng.Tests.TestFactory. 
Type A originates from 'nng.NETCore, Version=0.0.1.0, Culture=neutral, PublicKeyToken=null' in the context 'Default' at location '/Users/jake/nng.NETCore/tests/bin/Debug/netcoreapp2.1/nng.NETCore.dll'. 
Type B originates from 'nng.NETCore, Version=0.0.1.0, Culture=neutral, PublicKeyToken=null' in the context 'Default' at location '/Users/jake/nng.NETCore/tests/bin/Debug/netcoreapp2.1/nng.NETCore.dll'.' 
```

Enter fullscreen mode Exit fullscreen mode

不同的负载环境，不同的类型。

我在我的测试项目中引用了`nng.NETCore`程序集(包含 pinvokes ),并试图在这里加载它。我怎么能用一个我不知道的类型？这是一个我从来不用的 C#特性的机会， [`dynamic`](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/using-type-dynamic) :

```
dynamic factory = Activator.CreateInstance(type);
//...
var pushSocket = factory.CreatePusher(url, true); 
```

Enter fullscreen mode Exit fullscreen mode

测试通过，在我期望的大多数地方命中断点(无论是 VS 代码还是 VS for Mac 都不能通过`dynamic`命中断点，但是如果我设置了`DYLD_PRINT_LIBRARIES`，我的程序集明显不在:

```
dyld: loaded: /usr/local/share/dotnet/shared/Microsoft.NETCore.App/2.1.2/System.Globalization.Native.dylib
dyld: loaded: /usr/local/share/dotnet/shared/Microsoft.NETCore.App/2.1.2/System.Native.dylib
Microsoft (R) Test Execution Command Line Tool Version 15.7.0
Copyright (c) Microsoft Corporation. All rights reserved.

dyld: loaded: /usr/local/share/dotnet/shared/Microsoft.NETCore.App/2.1.2/System.Security.Cryptography.Native.Apple.dylib
Starting test execution, please wait...

Total tests: 1\. Passed: 1\. Failed: 0\. Skipped: 0.
Test Run Successful.
Test execution time: 1.3259 Seconds
dyld: unloaded: /usr/local/share/dotnet/shared/Microsoft.NETCore.App/2.1.2/libhostpolicy.dylib
dyld: unloaded: /usr/local/share/dotnet/shared/Microsoft.NETCore.App/2.1.2/libhostpolicy.dylib 
```

Enter fullscreen mode Exit fullscreen mode

貌似`AssemblyLoadContext.LoadFrom*()`不用`dyld`？嗯……不确定。

很明显，我不想到处都用`dynamic`。我重构了事物；删除对 pinvoke 组件的测试组件引用，并引入一个“中间人”/glue 组件，其中包含两者都使用的接口:

| 装配 | 项目参考 | 动态加载 | 笔记 |
| --- | --- | --- | --- |
| "测试" | 接口 | 平沃克 | 单元/集成测试 |
| "接口" |  |  | 使用 P/Invoke 的高级类型 |
| “pinvoke” | 接口 |  | P/Invoke 方法和使用它们的包装类型 |

这使我能够写出非常理智的:

```
[Fact]
public async Task PushPull()
{
    var alc = new ALC();
    var assem = alc.LoadFromAssemblyName(new System.Reflection.AssemblyName("nng.NETCore"));
    var type = assem.GetType("nng.Tests.TestFactory");
    IFactory<NngMessage> factory = (IFactory<NngMessage>)Activator.CreateInstance(type);
    var pushSocket = factory.CreatePusher("ipc://test", true); 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以从我喜欢的任何地方加载本地二进制文件。

出于好奇，我想知道是否可以添加一个对 pinvoke 的引用，并在本地库被成功调用后直接使用它:

```
Native.Msg.UnsafeNativeMethods.nng_msg_alloc(out var msg, UIntPtr.Zero); 
```

Enter fullscreen mode Exit fullscreen mode

没有:

```
nng.Tests.PushPullTests.PushPull [FAIL]
[xUnit.net 00:00:00.5527750] System.DllNotFoundException : Unable to load shared library 'nng' or one of its dependencies. In order to help diagnose loading problems, consider setting the DYLD_PRINT_LIBRARIES environment variable: dlopen(libnng, 1): image not found 
```

Enter fullscreen mode Exit fullscreen mode

“默认”加载上下文不了解本机库——它只存在于我的自定义上下文中。

我觉得可能有更简单的方法来达到我的目的。需要对此进行更多的调查。

## 表现

这些都不是“零成本”的。

NNanomsg 源代码引用了[这篇博客](http://ybeernet.blogspot.com/2011/03/techniques-of-calling-unmanaged-code.html)，其中提到了[SuppressUnmanagedCodeSecurity](https://docs.microsoft.com/en-us/dotnet/api/system.security.suppressunmanagedcodesecurityattribute)属性可能会显著提高性能。文档中的安全含义并不清楚，但听起来它可能只与操作系统资源有关；它不会比从本机代码调用库更不安全…

有[多种方法来操作消息](https://nanomsg.github.io/nng/man/v1.0.0/nng_msg.5.html)，我将用纯 C#来编写它们，以避免为琐碎的字符串操作做 pinvoke。