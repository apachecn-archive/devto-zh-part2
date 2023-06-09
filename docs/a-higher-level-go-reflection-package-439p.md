# 更高级的 Go 反射包

> 原文：<https://dev.to/progrium/a-higher-level-go-reflection-package-439p>

昨天，我做了一个快速的、计划外的流来启动一个 Go 包，它为 Go 值提供了一个更简单的反射 API。Go 有一个很棒的反射包，但是像调用一个方法或者获取一个属性这样的基本操作并不像它应该做的那样简单。此外，反射的本质意味着这通常需要大量的实验才能做好。至少对我来说是这样，我和 Go reflection 一起工作了很长时间。

想法是提供一个看起来更像 syscall/js API 的 API，用于与 WebAssembly 主机的 JavaScript 值进行交互。除了更简单之外，这个 API 还有获取和设置属性以及调用方法的方法。这些是反射中最常见的一些操作，但是使用反射本身并不只是给你这些。

原来如此。反射包的一个简单的高级包装器。这可以用来更容易地使 JavaScript 代理对象在 WebAssembly 中获得值，或者通过 RPC 公开值，这两种情况我都希望能做到。但是，我也经常使用反射，这样可以节省很多时间来记住如何做一些基本的操作。

总之，我完成了有趣方法的简单版本，以及一些基本测试。我可能会更多地在离线状态下工作，或者在需要使用它的项目中工作。

[https://www.youtube.com/embed/19azPetfH6o](https://www.youtube.com/embed/19azPetfH6o)