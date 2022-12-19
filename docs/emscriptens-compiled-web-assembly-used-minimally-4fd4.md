# Emscripten 编译的 Web 程序集，使用最少

> 原文：<https://dev.to/chromiumdev/emscriptens-compiled-web-assembly-used-minimally-4fd4>

(这是我关于在浏览器中使用 Wuffs 解码 gif 的帖子的技术对应物。如果你想看快速 GIF 解码，请点击这里！🖼️➡️👍)

将 C/C++转换成 Web 汇编(WASM)并不容易。截至 2018 年 7 月，事实上的工具链是 [Emscripten](https://github.com/kripken/emscripten) ，这是一套为我们工作的令人惊叹但富有挑战性的工具。当 Emscripten 在您的代码上运行时，它会生成一些东西:

*   编译后的`.wasm`文件本身——包含您的代码和运行时
*   除此之外还有样板文件
*   (可选)用于运行程序的`.html`线束

对于一个普通的构建来说，`.js`文件——运行 WASM 时包含的文件——可能有 100k 左右。除了明显的膨胀，为什么这是一件坏事？

*   它在你的全局范围内增加了一个单独的`Module`变量——不适合库，只适合单一的应用——文档甚至[称之为](https://kripken.github.io/emscripten-site/docs/getting_started/FAQ.html#can-i-use-multiple-emscripten-compiled-programs-on-one-web-page)
*   从 WASM 调用 JS 回来需要`EM_ASM`宏，调用全局(！)JS 方法——不适合模块化代码并隐藏 JS 习惯用法，如`.bind(this)`
*   它为`.wasm`执行自己的`window.fetch`——在 Node 中不理想，或者如果您想在一个文件中包含所有内容🙅
*   从主观上来说，它很难阅读/解析/修改。

## 平心而论

Emscripten 生成的样板文件*并非*不合理 Web Assembly 格式实际上相当简单，因此即使是拥有“堆”的概念，或者处理字符串或复杂数据类型，也是编译器需要*做的事情*。这里没有内置的概念，Emscripten 帮助您快速移动*。😲*

 *但是它所做的一切也可能是笨拙的。在这里，我将把它记录下来，这样您就可以自己实例化`.wasm`了。但是要小心！这不提供任何的`EM_`..魔术，像绑定，在 JavaScript 中暴露 C++对象等。这种糖有时很棒，但它是有代价的。如果你正在包装简单的 C 语言(或者甚至是 C++，你可以包装),那么你不需要它。

## 第一步

让我们构建一个简单的 C 库，它读取一个 PNG 文件并用元数据填充一个传递的`struct`，并通过一个`extern`方法回调 JavaScript。这是一个完全人为的演示，展示了一些具有挑战性的任务:

*   向 WASM 传递任意大小的数据，以及使用`malloc`和`free`
*   处理 c struts
*   回调到 JS
*   返回字符串错误描述。*