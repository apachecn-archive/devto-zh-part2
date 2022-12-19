# Vulkan 内存分配器 2.1.0

> 原文：<https://dev.to/reg__/vulkan-memory-allocator-210-27ih>

昨天我将过去几个月我一直在做的 **Vulkan 内存分配器**的代码变化合并到“主”分支，我认为这是一个重要的里程碑，所以我将其标记为**版本 2.1.0-beta.1** 。有许多新功能，包括:

*   添加了线性分配算法，可用于自定义池，可用作一次性释放、堆栈、双堆栈或环形缓冲区。
*   添加了记录对文件的库调用序列并使用专用应用程序重放的功能。
*   改进了对非一致性内存的支持。
*   改进了与检测不正确的映射内存使用相关的调试功能。
*   更改了 JSON 转储的格式，以包含更多信息，并允许在 VmaDumpVis 中更好地着色。

该版本还包括许多较小的错误修复，改进和补充。一切都经过测试并记录在案。然而，我称之为“测试”版本，以鼓励你在你的项目中测试它，并给我你的反馈。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[GPUOpen-LibrariesAndSDKs](https://github.com/GPUOpen-LibrariesAndSDKs)/[VulkanMemoryAllocator](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator)

### 易于集成的 Vulkan 内存分配库

<article class="markdown-body entry-content container-lg" itemprop="text">

# Vulkan 内存分配器

易于集成的 Vulkan 内存分配库。

**文档:**参见 [Vulkan 内存分配器](https://gpuopen-librariesandsdks.github.io/VulkanMemoryAllocator/html/)(由 [src/vk_mem_alloc.h](https://raw.githubusercontent.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/master/src/vk_mem_alloc.h) 中 Doxygen 风格的注释生成)

**执照:**麻省理工。参见 [LICENSE.txt](https://raw.githubusercontent.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/master/LICENSE.txt)

**Changelog:** 见 [CHANGELOG.md](https://raw.githubusercontent.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/master/CHANGELOG.md)

**产品页面:**[GPU open 上的 Vulkan 内存分配器](https://gpuopen.com/gaming-product/vulkan-memory-allocator/)

**构建状态:**

*   Windows: [![Build status](../Images/3bff058f5d04afc8eb8fa579017cdef9.png)](https://ci.appveyor.com/project/adam-sawicki-amd/vulkanmemoryallocator/branch/master)
*   Linux:[![Build Status](../Images/a242593e2ad8b9ccd1eee00a717772ea.png)T2】](https://travis-ci.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator)

# 问题

Vulkan 中的内存分配和资源(缓冲区和图像)创建非常困难(与旧的图形 API 相比，如 D3D11 或 OpenGL ),原因如下:

*   它需要大量的样板代码，就像 Vulkan 中的其他东西一样，因为它是一个低级的高性能 API。
*   还有额外的间接层:`VkDeviceMemory`是与创建`VkBuffer` / `VkImage`分开分配的，它们必须绑定在一起。
*   必须向驱动程序查询支持的内存堆和内存类型。不同的 ihv 提供不同类型的信息技术。
*   建议分配更大的内存块，并将其中的一部分分配给特定的资源。

# 特征

这个库可以帮助游戏开发者…

</article>

[View on GitHub](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator)