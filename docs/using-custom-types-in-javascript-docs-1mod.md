# 在 JavaScript 文档中使用自定义类型

> 原文：<https://dev.to/4lch4/using-custom-types-in-javascript-docs-1mod>

嘿 dev 社区，

我又带着另一个问题来找你，这个问题和我上一个关于 gif 的问题不太一样😅我好奇的是，用 Visual Studio 代码用 JavaScript 编写方法文档时，如何处理引用自定义类型。

我确信这不是描述它的最佳方式，所以我将表明我的意思。举下面这个例子:

```
// Used for JSDocs
const Window = require('vscode').window

/**
 * @typedef {Object} MessageOptions
 * @property {boolean} modal Indicates that this message should be modal.
 */

/**
 *
 * @param {Window} window
 * @param {string} text
 * @param {MessageOptions} [options]
 * @param {*[]} [items]
 */
module.exports.showMessage = (window, text, options = false, items = false) => {
  if (options && items) return window.showInformationMessage(text, options, items)
  else if (items) return window.showInformationMessage(text, items)
  else return window.showInformationMessage(text)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我为了帮助我的新项目 [Enmeti](https://github.com/HF-Solutions/Enmeti) 而创建的一个文件。对于它导出的一个方法，我有两个自定义参数，`MessageOptions`和`Window`。

对于`Window`,**vs code**模块导出了一个[窗口](https://code.visualstudio.com/docs/extensionAPI/vscode-api#_window)名称空间，这样我就可以将其作为变量引用，然后在注释中使用它。另一方面，`MessageOptions`没有被导出，所以我创建了一个自定义定义并引用它。

虽然我对`MessageOptions`的定义很满意，但我不太喜欢仅仅为了在引用方法时有可用的参数信息而为`Window`创建变量引用。这是我在我的一些项目中做过的事情，但我知道*有*是做这件事的更好方式。

有什么建议吗？