# 为什么 glTF 2.0 这么牛逼！

> 原文：<https://dev.to/ybalrid/why-gltf-20-is-awesome-2khp>

*原文发布于此:[https://blog . ybal rid . info/2018/07/16/why-gltf-2-0-is-awesome/](https://blog.ybalrid.info/2018/07/16/why-gltf-2-0-is-awesome/)T3】*

在处理多个 3D 相关软件时，有一件事让我感到非常沮丧:让它们交换 3D 资产。

你不能保证从一个软件中取出的东西在其他软件中看起来是一样的(例如游戏引擎)。你可能用米工作，发现虚幻用厘米工作。他们可以为纹理使用不同的对话，材质定义可能只是“不起作用”…)

总的来说，有比例问题，动画问题，纹理绑定问题，材质问题。

所有这些通常都要用一大堆可怕的导入/导出脚本来处理。您需要在工具链中运行的工具越多，情况就越糟糕

尽管如此，在这个行业中有许多或多或少标准化的文件格式，但没有一个真正适合实时渲染/视频游戏(以及扩展到 VR)的所有用例。

其中大多数更适合作为“创作”格式:3D 建模程序的文件，如 Autodesk 的 FBX 或 khronos group 的 Collada。它们很复杂，而且它们通常在如何实现和如何表示特定事物方面过于灵活，从编程的角度来看，它们包含的数据太多了。

FBX 似乎是一个 de-factor 标准，尤其是在视频游戏行业，因为 Autodesk 软件作为建模动画工具的优势。当他们不使用 3Ds Max 时，他们使用的是 Maya…！；-)

一般来说，你想要做的是将这些资产转换成可以快速加载到游戏引擎中的序列化二进制文件。这通常是基于每个引擎来完成的。据我所知,“将东西放入 Unity”的一般工作流程通常包括 put(例如)。FBX 文件到项目目录中，当 Unity 构建你的游戏时，它会将每一个资源转换成针对目标平台优化的格式。

尽管如此，这种重新发明轮子并为 3D 资产的每个目标应用程序重新发明一种“传输格式”的方法似乎并不正确。

在 2D 世界，当整个行业基本上围绕 2D 图片的 JPEG 文件格式进行标准化时，这个问题得到了解决。每一台相机都输出 JPEG 编码和压缩的图像文件(特例或高级用户除外)。

显然，JPEG 文件没有 Photoshop“项目”(PSD)文件包含的所有细节，但 JPEG 不是用于创作图像，而是用于共享图像。

我们需要的是一个“用于 3D 的 JPEG”，这正是 Khronos 小组在开放社区的努力下开发并发布的“OpenGL 传输格式”，该规范的 2.0 版本于 2017 年 6 月发布。

[![](img/d9be33f8e51bbfeb73cfddc0799c02b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WfPB2cHO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.ybalrid.info/wp-content/uploads/2018/01/glTF_500px_June16-300x150.png)

这种格式允许以标准格式描述 3D 对象(甚至包括带有灯光、摄像机和动画整个场景)。例如，与 Collada 相反，在 glTF 中没有解释的余地，也没有描述同一事物的两种方式

glTF 将模型数据表示为简单二进制缓冲区。“访问器”允许根据上下文解释数据。这特别适用于在 OpenGL 中加载这些文件，您可以使用 glBufferData 将每个缓冲区加载到 GPU 中，然后使用 glVertexAttribPointer 解析每个访问器，以绑定到缓冲区中每个顶点元素的位置。

glTF 基于一个用 JSON 编写的简单场景图描述，以及大量原始资源，如二进制缓冲区和作为图像的纹理数据。

该标准在其纯文本中通常容易理解，但使用的一些概念通过一些图形更容易理解。谢天谢地，有一个很棒的“glTF 是什么鸭子？”在官方存储库上:

[![glTF overview poster](img/7810453c946869bf36cd84b20aa90787.png)T2】](https://raw.githubusercontent.com/KhronosGroup/glTF/master/specification/2.0/figures/gltfOverview-2.0.0a.png)

这张海报描述了你需要了解的关于 glTF 文件格式的大部分内容

目前有大量支持 glTF 的软件、工具和库。我希望将来会更好。

这种格式是由 khronos group 开发的，同时还有 blender 的 exporter 的官方实现，它已经得到了业界的大量支持

[![](img/afe9757c2227067265992cbf2682fa4d.png)T2】](https://www.khronos.org/assets/uploads/apis/2017-gltf-20-launch-3_10.jpg)

glTF NASCAR jacked 有一些真正重要的赞助商！

我不认为我真的需要发展更多，为什么我认为为 glTF 争取更多支持的一切努力将有益于整个行业/社区，但今天支持 glTF 应该是你的**转到**选项。

Godot 的主要开发者 Juan Linietsky 实际上写了一篇[的长文](https://godotengine.org/article/we-should-all-use-gltf-20-export-3d-assets-game-engines)来比较多种格式，结论是我们都应该支持 glTF2，我完全同意！

在我的下一篇文章中，我将讲述用 C++为流行的开源 Ogre 渲染引擎编写 glTF 导入插件的过程。它目前是一个正在进行中的项目，但是它开始变得可用，并且可以在这里找到 T2