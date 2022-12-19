# 当集成显卡工作得更好时

> 原文：<https://dev.to/reg__/when-integrated-graphics-works-better-lek>

在 RPG 游戏中，你的角色越强大，你要对抗的怪物就越强悍和可怕。我有时觉得这同样适用于现实生活——当你是程序员时遇到的 bug。我最近写了一篇关于 QueryPerformanceCounter 调用需要很长时间的问题[的博客。我刚刚遇到了另一个奇怪的问题。以下是我的故事:](http://asawicki.info/news_1667_when_queryperformancecounter_call_takes_long_time.html)

我有联想 IdeaPad G50-80 (80E502ENPB)笔记本电脑。它具有可切换的显卡:集成的英特尔 i7-5500U 和专用的 AMD 镭龙 R5 M330。当然我以前选择 AMD 专用显卡，因为更强大。我的应用程序是一个音乐可视化程序。它使用 Direct3D 11 渲染图形。它使用一个`ID3D11Device`对象和一个线程进行渲染，但在两个输出上显示两个窗口:输出 1(笔记本电脑屏幕)包含带有 GUI 和预览的窗口，而输出 2(通过 VGA 或 HDMI 连接的投影仪)使用无边框、最上面的窗口显示主视图，覆盖整个屏幕(但不是真正的全屏，如`IDXGISwapChain::​SetFullscreenState`)。我倾向于在输出 1 ( `IDXGISwapChain::​Present SyncInterval = 1`)启用垂直同步，在输出 0 ( `SyncInterval = 0`)禁用垂直同步。我的渲染算法是这样的:

```
Loop over frames:
    Render scene to MainRenderTarget
    Render MainRenderTarget to OutputBackBuffer, covering whole screen
    Render MainRenderTarget to PreviewBackBuffer, on a quad
    Render ImGui to PreviewBackBuffer
    OutputSwapChain->Present()
    PreviewSwapChain->Present() 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我只有一个问题:我的帧速率随着时间的推移而下降。它曾经在启动应用程序后从 60 到 30 FPS 快速下降并稳定下来，但几个小时后，它稳步下降到 20 FPS 甚至更低。我在代码中找不到原因，比如内存泄漏。这似乎与渲染有关。我能以某种方式忍受这个问题——低帧率并不那么明显。

本周四，当我想测试新版本的程序时，我突然意识到它在启动大约一分钟后就挂起了。这是一个奇怪的情况，应用程序似乎运行正常，但它只是没有渲染任何新的帧。通过用[进程黑客](http://processhacker.sourceforge.net/)检查 CPU 使用和线程列表，我可以看到它仍然工作。我可以最小化它的窗口或用其他窗口覆盖它们，它们在恢复后保留了它们的内容。我甚至在 [GPUView](https://graphics.stanford.edu/~mdfisher/GPUView.html) 中捕捉到了 trace，才注意到 app 正在填充 DirectX 命令队列，AMD GPU 正在工作。然而，什么也没有渲染。

这对我来说是一个可怕的情况，因为我需要让它在这个周末发挥作用。在我检查了重启应用程序或整个系统没有帮助之后，我试图找出原因并以各种方式修复它:

1.  我想可能是我的程序的新版本中有一些错误，所以我启动了以前的版本——一个以前成功工作过的版本，达到了 10 个多小时的正常运行时间。不幸的是，问题仍然出现。

2.  我想这可能是新的 AMD 显卡驱动程序的错误，所以我下载并安装了以前的版本，执行“干净安装”。也无济于事。

3.  绝望中，我格式化了整个硬盘并重新安装了操作系统。无论如何，我打算用它，因为它是一个 3 年前的系统，从 Windows 8 升级而来，我还有一些其他问题(我在这里不描述，因为它们与图形无关)。我安装了最新的、干净的 Windows 10，带有最新的更新和所有的驱动程序。即使这样也没有解决我的问题。每次启动后不久，程序仍然挂起。

我终于想到了一个主意，将我的应用程序切换到使用英特尔集成显卡。这可以在镭龙设置>可切换图形选项卡中完成。在特定可执行文件的弹出菜单中，“高性能”意味着选择专用 AMD GPU，“节能”意味着选择集成英特尔 GPU。参见文章[配置笔记本电脑可切换显卡...](https://support.amd.com/en-us/kb-articles/Pages/DH-017.aspx)详情。

它解决了我的问题！程序不仅不再挂起，而且现在还保持稳定的 60 FPS(至少在我 2h 的测试中是这样)。只有当全高清输出中有许多图层混合在一起的场景时，帧率才会下降——显然这个 GPU 无法跟上每秒绘制这么多像素的速度。无论如何，这是使用集成英特尔显卡比使用更快的专用 GPU 效果更好的情况。

我还不知道这个奇怪的 bug 是什么原因造成的。我的应用程序使用 D3D11 的方式有问题吗？还是显卡驱动的 bug(我需要安装的两个之一)？当我有时间的时候，我想要进一步调查它。目前，我倾向于相信:

*   最近唯一可能改变并破坏我的应用程序的是微软推出的一些 Windows 更新。

*   这两个问题:我之前遇到的帧速率随时间下降的问题和图像完全冻结的新问题是相关的。它们可能与可切换图形有关——系统中有两个不同的 GPU，两者同时启用。我怀疑，也许当我想使用镭龙时，输出(或其中之一)无论如何都要连接到英特尔，因此需要复制映像并与英特尔驱动程序同步。