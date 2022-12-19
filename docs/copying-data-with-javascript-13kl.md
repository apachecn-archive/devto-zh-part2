# 用 JavaScript 复制数据

> 原文：<https://dev.to/itachiuchiha/copying-data-with-javascript-13kl>

[这篇文章最初发表在我的博客上](https://medium.com/ali-g%C3%B6ren/copy-something-with-javascript-b8604f51aad3)

大家好。在这篇文章中，我将告诉你如何用 JavaScript 复制一些东西。

在开始之前，我需要说，有一个 npm 包是用本文中的信息构建的。

你可以安装它来测试

[https://www.npmjs.com/package/copy-simple](https://www.npmjs.com/package/copy-simple)T2】

```
npm i copy-simple 
```

Enter fullscreen mode Exit fullscreen mode

在[这个](https://github.com/aligoren/copy-simple)链接上还有一个 GitHub 库。

我将创建一个名为 **copy** 的空函数。我们将使用这个函数来复制值、元素文本、HTML 或选定的文本。

```
function copy(el) {
   // We will create different functions
} 
```

Enter fullscreen mode Exit fullscreen mode

**el** 参数将用于通过 [querySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) 方法处理元素。也就是说，我们的方法会改变。

```
function copy(el) {
  var e = document.querySelector(el);

  function saveClipBoard(data) {

  }

  function val() {

  }

  function text() {

  }

  function html() {

  }

  function url() {

  }

  function selected() {

  }

  return {
    val: val,
    text: text,
    html: html,
    url: url,
    selected: selected
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有些新方法什么都不包含。但是我们的方法看起来就像这样。如您所见，我们的方法将返回一个包含方法的对象。

## 创建要复制的临时 HTML 元素

为了复制一些东西，我们需要创建一个输入元素。也许有不同的方法可以做到这一点。但是在复制值之后，我们将删除这个元素。为此，我们将使用 **saveClipBoard** 方法。

## [T1】创建 saveClipBoard 方法](#creating-the-saveclipboard-method)

我们的方法将是这样的；

```
function saveClipBoard(data) {
  var dummy = document.createElement('input');
  var text = data;

  document.body.appendChild(dummy);
  dummy.value = text;
  dummy.select();
  var success = document.execCommand('copy');
  document.body.removeChild(dummy);

  return success;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，我们将数据参数的值赋给虚拟元素的值。之后，我们使用了 [execCommand](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) 方法。该方法将返回一个布尔值。在此方法之后，我们将移除虚拟元素。你甚至可以像这样试试。

## 获取数值

其他方法会是这样的

```
function val() {
  var v = e.value
  return saveClipBoard(v)
}

function text() {
  var t = e.innerText

  return saveClipBoard(t)
}

function html() {
  var h = e.outerHTML

  return saveClipBoard(h)
}

function url() {
  var u = window.location.href

  return saveClipBoard(u)
}

function selected() {
  var s = window.getSelection().toString()

  return saveClipBoard(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

这些方法很容易理解。每个方法都调用 **saveClipBoard** 方法。有一种不同的方法叫做 [getSelection](https://developer.mozilla.org/en-US/docs/Web/API/Window/getSelection) ，你可能不知道。Mozilla 说:

> 返回一个 Selection 对象，该对象代表用户选定的文本范围或插入符号的当前位置。

仅此而已。我们的方法完成了。

```
function copy(el) {
  var e = document.querySelector(el);

  function saveClipBoard(data) {
    var dummy = document.createElement('input');
    var text = data;

    document.body.appendChild(dummy);
    dummy.value = text;
    dummy.select();
    var success = document.execCommand('copy');
    document.body.removeChild(dummy);

    return success;
  }

  function val() {
    var v = e.value

    return saveClipBoard(v)
  }

  function text() {
    var t = e.innerText

    return saveClipBoard(t)
  }

  function html() {
    var h = e.outerHTML

    return saveClipBoard(h)
  }

  function url() {
    var u = window.location.href

    return saveClipBoard(u)
  }

  function selected() {
    var s = window.getSelection().toString()

    return saveClipBoard(s)
  }

  return {
    val: val,
    text: text,
    html: html,
    url: url,
    selected: selected
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 来测试一下吧

我已经为你在代码笔上创建了一支笔。为了测试我们的方法，你需要点击按钮。

[https://codepen.io/aligoren/embed/oPOYmR?height=600&default-tab=result&embed-version=2](https://codepen.io/aligoren/embed/oPOYmR?height=600&default-tab=result&embed-version=2)

您可以使用注释块中的其他方法。

希望这篇文章对你有帮助。

感谢阅读。