# web 会超越 JavaScript 吗？

> 原文：<https://dev.to/nektro/will-the-web-ever-evolve-past-javascript--3bgi>

第一次碰到[的时候。NET Framework](https://en.wikipedia.org/wiki/.NET_Framework) 我真的很惊讶。微软开发了一种完全与平台无关的伪汇编语言，作为众多语言(C#、F#等)的编译器目标。如果你一直在关注 WebAssembly 的最新发展，这可能听起来非常熟悉。

WebAssembly 产生了一个伟大的 MVP，但对于 WASM 的真正腾飞，我要说的是这个问题是最重要的。本期描述了与现有 es 模块的互操作性，并让 WASM 能够访问大家都知道并喜爱的 Web APIs。有了这个，你可以用*任何*语言*编写一个 web 应用程序。

你怎么想呢?WASM 应该坚持做数学，还是应该让网络拥有自己的[框架类库](https://en.wikipedia.org/wiki/Framework_Class_Library)？