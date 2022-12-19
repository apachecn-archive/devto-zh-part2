# 调试 D3D12 驱动程序崩溃

> 原文：<https://dev.to/reg__/debugging-d3d12-driver-crash-1c88>

新一代 explcit 图形 API(Vulkan 和 DirectX 12)效率更高，涉及的 CPU 开销更少。部分原因是他们没有检查大多数错误。在旧的 API(direct 3d 9，OpenGL)中，每个函数调用都在内部进行验证，返回成功或失败代码，而驱动程序崩溃则表明驱动程序代码中存在错误。另一方面，新的 API 依赖于开发者做正确的事情。当然，有些函数仍然会返回错误代码(尤其是那些分配内存或创建一些资源的函数)，但那些将命令记录到命令列表中的函数只是返回`void`。如果你做了一些非法的事情，你可以期待未定义的行为。您可以使用验证层/调试层来进行一些检查，但在其他情况下，在某些 GPU 上一切都可以正常工作，您可能会得到不正确的结果，或者您可能会遇到驱动程序崩溃或超时(称为“TDR”)。好的一点是(与旧的 Windows XP 相反)，图形驱动程序内部崩溃不会导致“蓝屏死机”或机器重启。系统只是重启图形硬件和驱动程序，而你的程序从`IDXGISwapChain::​Present`这样的函数中接收`DXGI_ERROR_DEVICE_REMOVED`代码。不幸的是，您不知道哪个特定的 draw 调用或其他命令导致了崩溃。

NVIDIA 提出了解决方案:他们创建了 NVIDIA affairs 库。它允许您(除了其他事情之外)记录将自定义“标记”数据写入缓冲区的命令，该缓冲区在驱动程序崩溃后仍然存在，因此您可以稍后读取它，并查看最后成功执行的是哪个命令。不幸的是，这个库只能用于 NVIDIA 显卡。

前段时间我在我的帖子里展示了一个针对 Vulkan 的便携解决方案:[“调试 Vulkan 驱动崩溃——相当于 NVIDIA 余波”](http://asawicki.info/news_1677_debugging_vulkan_driver_crash_-_equivalent_of_nvidia_aftermath.html)。现在我想介绍一个 Direct3D 12 的解决方案。事实证明，这个 API 也提供了一种标准化的方法来实现这一点，其形式是方法[id 3d 12 graphicscommandlist 2::WriteBufferImmediate](https://docs.microsoft.com/en-us/windows/desktop/api/d3d12/nf-d3d12-id3d12graphicscommandlist2-writebufferimmediate)。一个警告:这个新版本的接口需要:

*   用户至少要有 Windows 10 Fall Creators 更新。
*   开发者至少要为 Windows 10 秋季创造者更新提供 Windows SDK 版本。
*   开发人员使用 Visual Studio 2017 -此版本的 Windows SDK 需要。

我创建了一个简单的库，在 easy interface 下实现了所有需要的逻辑，我称之为 [**D3d12AfterCrash**](https://github.com/sawickiap/MISC/tree/master/D3d12AfterCrash) 。您可以在文件“D3d12AfterCrash.h”中找到所有详细信息和使用说明。

我想使用 WinAPI 函数`VirtualAlloc(NULL, bufferSize, MEM_COMMIT, PAGE_READWRITE)`分配缓冲区会更好，然后调用`ID3D12Device3::​OpenExistingHeapFromAddress`和`ID3D12Device::​CreatePlacedResource`，但是我简单的方法`ID3D12Device::​CreateCommittedResource`似乎有效——缓冲区在驱动程序崩溃后仍然存在并保留其内容。我在 AMD 和 NVIDIA 卡上都检查过。