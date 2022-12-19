# 使用 Hammerspoon 的 MacOS 窗口管理

> 原文：<https://dev.to/dploeger/macos-window-management-using-hammerspoon-20mc>

使用具有各种可用屏幕配置的 macOS 笔记本电脑很麻烦。MacOS 尽最大努力将应用程序窗口重置为屏幕上次连接的状态，但一些供应商似乎找到了破坏该功能的方法。

而且目前还没有一个好的窗口管理器可以支持在多个屏幕上的多个空间上的窗口。

考虑到这一点，[锤勺](https://www.hammerspoon.org/)进入状态。Hammerspoon 是一个自动化框架，基本上是 macOS 核心管理功能(如键盘和鼠标操作、窗口管理等)的桥梁。)到 [Lua](https://www.lua.org/) 。

有一个单一的配置文件可以做各种事情。此外，它还有一个允许空间管理的库，这似乎仍然是应用程序的一个大问题。

所以我写了一些 Lua 代码来做我的窗口管理工作:

*   身份应用程序窗口
*   把它们放在右边的屏幕上
*   把它们放在正确的地方
*   最大化它们

为了给别人参考，也为了给自己做个备份，我把它发表在了 Github 上。

玩得开心。

[PDF](https://dev.to/feed/?pdf=770)