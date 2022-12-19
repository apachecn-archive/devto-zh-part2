# 使用 Go 插件编写模块化软件

> 原文：<https://dev.to/dlion/writing-modular-software-using-go-plugins-2c28>

Go 的 1.8 版本带来了使用插件编写模块化软件的能力，如何实现呢？

每个插件都编译成可以在运行时加载的共享对象库，很有趣吧？让我们来看看它是如何工作的！