# 有一种方法可以在 Vulkan 中查询 GPU 内存使用情况-使用 DXGI

> 原文：<https://dev.to/reg__/there-is-a-way-to-query-gpu-memory-usage-in-vulkan---use-dxgi-1f0d>

在我的 GDC 2018 演讲“Vulkan 和 DX12 中的内存管理”([幻灯片免费提供](https://gpuopen.com/gdc-2018-presentations/)，GDC Vault 付费墙后面的视频)中，我说过在 Direct3D 12 中，你可以查询已使用和可用的 GPU 内存的确切数量，而在 Vulkan 中没有办法做到这一点，所以我建议只查询内存容量(`VkMemoryHeap::size`)，并将你的使用率限制在 80%左右。事实证明我并不完全正确。如果您为 Windows 编写代码，有一种方法可以做到这一点。我假设提到的函数`IDXGIAdapter3::QueryVideoMemoryInfo`是 Direct3D 12 接口的一部分，而它实际上是 DirectX 图形基础设施(DXGI)的一部分。这是一个更通用、更高级别的 Windows API，它允许您枚举系统中安装的适配器(图形卡)，查询它们的参数和连接到它们的输出(监视器)。Direct3D 指的就是这个 API，但又不一样。

关键问题是:用 Vulkan 做图形的时候能不能用 DXGI 查询 GPU 内存使用情况，而不是 D3D11 或者 D3D12？它会返回一些合理的数据而不是全零吗？简短的回答是:是的！我做了一个实验——我写了一个简单的应用程序，创建各种 Vulkan 对象并查询 DXGI 的内存使用情况。结果看起来很有希望。但是在我开始讨论细节之前，这里有一个简短的入门指南，向所有非 DirectX 开发人员介绍如何使用这个 DXGI 接口:

1:在 Visual Studio 中使用 C++。你也可以使用一些其他的 Windows 编译器或者其他编程语言，但是可能会比较难设置。

2:安装相对较新的 Windows SDK。

3: `#include <dxgi1_4.h>`和`<atlbase.h>`

4:用“dxgi.lib”链接。

5:创建工厂对象:

```
IDXGIFactory4* dxgiFactory = nullptr;
CreateDXGIFactory1(IID_PPV_ARGS(&dxgiFactory)); 
```

最后别忘了发布:

```
dxgiFactory->Release(); 
```

6:编写一个循环来枚举可用适配器。选择并记住合适的。

```
IDXGIAdapter3* dxgiAdapter = nullptr;
IDXGIAdapter1* tmpDxgiAdapter = nullptr;
UINT adapterIndex = 0;
while(m_DxgiFactory->EnumAdapters1(adapterIndex, &tmpDxgiAdapter) != DXGI_ERROR_NOT_FOUND)
{
    DXGI_ADAPTER_DESC1 desc;
    tmpDxgiAdapter>GetDesc1(&desc);
    if(!dxgiAdapter && desc.Flags == 0)
    {
        tmpDxgiAdapter->QueryInterface(IID_PPV_ARGS(&dxgiAdapter));
    }
    tmpDxgiAdapter->Release();
    ++adapterIndex;
} 
```

最后别忘了发布:

```
dxgiAdapter->Release(); 
```

请注意，使用新版本的 DXGI 接口，如`DXGIFactory4`和`DXGIAdapter3`，需要开发者端的 Windows SDK(否则无法编译)和用户端的 Windows 系统(否则函数调用失败并返回适当的`HRESULT`)的相对较新的版本(我不确定是哪一个)。

7:要查询此时的 GPU 内存使用情况，使用以下代码:

```
DXGI_QUERY_VIDEO_MEMORY_INFO info = {};
dxgiAdapter->QueryVideoMemoryInfo(0, DXGI_MEMORY_SEGMENT_GROUP_LOCAL, &info); 
```

有两种可能的选择:

*   `DXGI_MEMORY_SEGMENT_GROUP_LOCAL`是 GPU 的本地内存，所以基本上是显存。
*   `DXGI_MEMORY_SEGMENT_GROUP_NON_LOCAL`是系统 RAM。

在返回的结构成员中，最有趣的是`CurrentUsage`。它似乎准确地反映了 GPU 内存的使用——当我分配一个新的`VkDeviceMemory`对象时，以及当我通过创建其他 Vulkan 资源(如交换链、描述符池和描述符集、命令池和命令缓冲区、查询池等)来使用一些隐式内存时，它会增加。

视频内存的其他 DXGI 功能-预算更改通知(`IDXGIAdapter3::RegisterVideoMemoryBudgetChangeNotificationEvent`)和预订(`IDXGIAdapter3::SetVideoMemoryReservation`)的回调可能也适用于 Vulkan，但我没有检查它们。

例如，在我的 GPU = AMD 镭龙 RX 580 8 GB 和 16 GB 系统内存的系统上，在程序启动时和任何 Vulkan 或 D3D 初始化之前，DXGI 报告以下数据:

```
Local:
    Budget=7252479180 CurrentUsage=0
    AvailableForReservation=3839547801 CurrentReservation=0
Nonlocal:
    Budget=7699177267 CurrentUsage=0
    AvailableForReservation=4063454668 CurrentReservation=0 
```

8:您可能希望选择正确的 DXGI 适配器来匹配 Vulkan 中使用的物理设备。即使在只有一个独立 GPU 的系统上，也报告了两个适配器，其中一个是软件渲染器。我通过比较`desc.Flags == 0`将其排除，这意味着这是一个真实的硬件加速的 GPU，而不是`DXGI_ADAPTER_FLAG_REMOTE`或`DXGI_ADAPTER_FLAG_SOFTWARE`。

好消息是，即使系统中有更多这样的适配器，也有办法在 DXGI 和 Vulkan 之间进行匹配。这两个 API 都返回本地唯一标识符(LUID)。在 DXGI 里是在`DXGI_ADAPTER_DESC1::AdapterLuid`里。在 Vulkan 中是在`VkPhysicalDeviceIDProperties::deviceLUID`中。它们属于不同的类型——两个 32 位的数字和一个字节数组，但是简单的原始内存比较似乎工作正常。所以找到匹配 Vulkan 物理设备的 DXGI 适配器的方法是:

```
// After obtaining VkPhysicalDevice of your choice:
VkPhysicalDeviceIDProperties physDeviceIDProps = {
    VK_STRUCTURE_TYPE_PHYSICAL_DEVICE_ID_PROPERTIES };
VkPhysicalDeviceProperties2 physDeviceProps = {
    VK_STRUCTURE_TYPE_PHYSICAL_DEVICE_PROPERTIES_2 };
physDeviceProps.pNext = &physDeviceIDProps;
vkGetPhysicalDeviceProperties2(physicalDevice, &physDeviceProps);

// While enumerating DXGI adapters, replace condition:
// if(!dxgiAdapter && desc.Flags == 0)
// With this:
if(memcmp(&desc.AdapterLuid, physDeviceIDProps.deviceLUID, VK_LUID_SIZE) == 0) 
```

请注意，函数`vkGetPhysicalDeviceProperties2`需要 Vulkan 1.1，所以设置`VkApplicationInfo::apiVersion = VK_API_VERSION_1_1`。否则调用会导致“访问冲突”错误。

在我的下一篇博文中，我将展示我在 Vulkan 应用程序中使用 DXGI 的实验的详细结果，在 2 个不同的 GPU 上进行测试。请几天后回来...