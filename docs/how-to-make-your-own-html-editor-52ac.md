# 如何构建自己的所见即所得编辑器

> 原文：<https://dev.to/saigowthamr/how-to-make-your-own-html-editor-52ac>

你见过 WYSIWYG 编辑器吗今天我展示了简单版的 WYSIWYG 编辑器。

如果你熟悉 HTML 和 JavaScript，那么你就可以开始了。

所以让我们开始我们的简单版本
我不是在解释一切，而是重要的事情

首先，我们需要向 HTML 元素添加一个 [contentEditable](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable) 属性

```
 <div class="editor" contenteditable>
      <h1>Simple Html editor</h1>
      <p>Good to start</p>
    </div> 
```

现在，div 元素是可编辑的，我们的 HTML 文档公开了 **[execCommand](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand)** 。

让我们看看什么是 execCommand

```
document.execCommand(cmd,defaultUI,value); 
```

## execCommand 需要三个参数

**cmd** :意思是我们要告诉哪个命令需要执行。
举例:' bold '[commandlist](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand#Commands)

**defaultUI** :是否显示默认用户界面为布尔值。

**value** :我们需要给一些命令添加 value 参数。
例如:在 createLink cmd 中，我们需要在链接中添加一个引用 href 属性的值。

所以让我们在实践中使用 execCommand

现在我正在创建一个按钮 HTML 元素，并为它附加了一个事件监听器。

```
 <button class="tool-items fa fa-bold" 
  onclick="document.execCommand('bold', false, '');">
 </button> 
```

现在我们只建立了粗体命令检查完整版本如下。

[https://codepen.io/saigowthamr/full/OZmWqW/%20?height=500&default-tab=result&embed-version=2](https://codepen.io/saigowthamr/full/OZmWqW/%20?height=500&default-tab=result&embed-version=2)