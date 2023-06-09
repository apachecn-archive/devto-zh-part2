# 调试 Vulkan 驱动程序崩溃-相当于 NVIDIA 的后果

> 原文：<https://dev.to/reg__/debugging-vulkan-driver-crash---equivalent-of-nvidia-aftermath-1mlm>

新一代 explcit 图形 API(Vulkan 和 DirectX 12)效率更高，涉及的 CPU 开销更少。部分原因是他们没有检查大多数错误。在旧的 API(direct 3d 9，OpenGL)中，每个函数调用都在内部进行验证，返回成功或失败代码，而驱动程序崩溃则表明驱动程序代码中存在错误。另一方面，新的 API 依赖于开发者做正确的事情。当然，一些函数仍然会返回错误代码(尤其是那些分配内存或创建一些资源的函数)，但是那些将命令记录到命令缓冲区的函数只会返回`void`。如果你做了一些非法的事情，你可以期待未定义的行为。您可以使用验证层/调试层来进行一些检查，但在其他情况下，在某些 GPU 上一切都可以正常工作，您可能会得到不正确的结果，或者您可能会遇到驱动程序崩溃或超时(称为“TDR”)。好的一点是(与旧的 Windows XP 相反)，图形驱动程序内部崩溃不会导致“蓝屏死机”或机器重启。系统只是重启图形硬件和驱动程序，而你的程序从`vkQueueSubmit`这样的函数中接收到`VK_ERROR_DEVICE_LOST`代码。不幸的是，您不知道哪个特定的 draw 调用或其他命令导致了崩溃。

NVIDIA 提出了解决方案:他们创建了 NVIDIA affairs 库。它允许您(除了其他事情之外)记录将自定义“标记”数据写入缓冲区的命令，该缓冲区在驱动程序崩溃后仍然存在，因此您可以稍后读取它，并查看最后成功执行的是哪个命令。不幸的是，这个库只适用于 NVIDIA 显卡，并且只适用于 D3D11 和 D3D12。

我在为 Vulkan 寻找类似的解决方案。当我看到 Vulkan 可以“导入”外部存储器时，我想也许我可以使用函数`vkCmdFillBuffer`将立即值写入这样的缓冲区，这样可以实现相同的逻辑。然后我开始试验扩展:VK _ KHR _ 获取 _ 物理 _ 设备 _ 属性 _2，VK _ KHR _ 外部 _ 内存 _ 能力，VK _ KHR _ 外部 _ 内存，VK _ KHR _ 外部 _ 内存 _win32，VK _ KHR _ 专用 _ 分配。我基本上是试图以某种方式分配一块系统内存，并将其导入 Vulkan 以作为 Vulkan 缓冲区写入其中。我尝试了很多东西:`CreateFileMapping` + `MapViewOfFile`，`HeapCreate` + `HeapAlloc`等方式，打着各种旗号，但是没有一样对我有效。我也找不到任何关于如何在 Windows 中使用这些扩展来导入一些系统内存作为 Vulkan 缓冲区的描述或示例代码。

当我知道在 Vulkan 内部创建正常的设备内存和缓冲区就足够了时，一切都变了！它在驱动程序崩溃后仍然存在，所以它的内容可以在以后通过映射指针读取。不需要扩展。我不认为这是由规范保证的，但它似乎在 AMD 和 NVIDIA 卡上都有效。因此，我目前对在 Vulkan 驱动程序崩溃中幸存下来的编写人员的解决方案是:

1.  调用`vkAllocateMemory`从有`HOST_VISIBLE + HOST_COHERENT`标志的内存类型中分配`VkDeviceMemory`。(这是系统内存。Spec 保证你总能找到这样的类型。)
2.  使用`vkMapMemory`映射内存，以获取指向其数据的原始 CPU 指针。
3.  使用`VK_BUFFER_USAGE_TRANSFER_DST_BIT`调用`vkCreateBuffer`来创建`VkBuffer`，并使用`vkBindBufferMemory`将其绑定到内存。
4.  将命令记录到`VkCommandBuffer`时，使用`vkCmdFillBuffer`将带有自定义“标记”的即时数据写入缓冲区。
5.  如果一切顺利，关机期间不要忘记`vkDestroyBuffer`和`vkFreeMemory`。
6.  如果您遇到驱动程序崩溃(receive `VK_ERROR_DEVICE_LOST`)，读取指针下的数据，查看最后成功写入的标记值，并推断您的哪个命令可能导致崩溃。

在最新的 AMD 驱动上也有一个新的扩展:VK_AMD_buffer_marker。它只增加了一个功能:`vkCmdWriteBufferMarkerAMD`。它的工作方式与前面提到的`vkCmdFillBuffer`类似，但是它增加了两个好东西，让您可以以更好的粒度编写标记:

*   内外渲染过程都可以调用，而`vkCmdFillBuffer`必须调用外渲染过程。
*   它在指定的管道阶段完成执行后执行其写入。

我创建了一个简单的库，在 easy interface 下实现了所有这些逻辑。你所需要使用的只是这一个文件:[**vulkanaftercrash . h**](https://github.com/sawickiap/MISC/blob/master/VulkanAfterCrash.h)。