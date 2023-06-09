# 单例模式的有效用法(带有空对象模式)

> 原文：<https://dev.to/dance2die/a-valid-usage-of-singleton-pattern-with-null-object-pattern-3gfj>

<sub><sup>图片由 [Chetan Menaria](https://unsplash.com/photos/oxVjCyH_ldQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/loneliness?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</sup></sub>
辛格尔顿以反模式而臭名昭著。你可能已经被它烧坏了。

我从[编码块播客第 16 集](https://www.codingblocks.net/podcast/episode-16-design-patterns-part-2-oh-behave/)中的[迈克尔·奥特洛](https://www.codingblocks.net/author/outlaw/)那里了解到[单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)可以与[空对象模式](https://en.wikipedia.org/wiki/Null_object_pattern)结合使用。

让我们开始吧。

## 🤔为什么将空对象实现为单例对象？

《编码积木》第 16 集第 42 分钟，迈克尔·奥特洛解释了两个原因。

> 1.  "If you have two versions of empty objects, they should be the same. Why waste memory to duplicate the same empty objects".
> 2.  Used for equality check-every empty object can be compared by reference.

根据不同的情况，你可能甚至不需要做相等性检查，因为一个空对象通常什么也不做(正如你在下面的例子中看到的。)

**TL；你可以在这里停止阅读。**

## ✍️举例实现

下面是一个如何将单例模式与空对象模式结合使用的例子。
假设有一个简单的工厂(PaymentStrategyFactory ),它返回一个策略对象实例，用于处理给定提供者名称的支付。