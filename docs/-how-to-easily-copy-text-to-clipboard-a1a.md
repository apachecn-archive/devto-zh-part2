# 📋如何轻松地将文本复制到剪贴板

> 原文：<https://dev.to/mornir/-how-to-easily-copy-text-to-clipboard-a1a>

Chrome 团队最近扩展了对剪贴板 API 的支持。一个改进是能够轻松复制 DOM 中不存在的文本:

```
let textToCopy = 'Copy me' 

async function copyToClipboard() {
  try {
    // 1) Copy text
    await navigator.clipboard.writeText(textToCopy);

    // 2) Catch errors
  } catch (err) {
    console.error('Failed to copy: ', err);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

新方法`navigator.clipboard.writeText`确实比常用的`document.execCommand('copy')`方法:
好用得多

```
let textToCopy = 'Copy me' 

function copyToClipboard() {
  // 1) Add the text to the DOM (usually achieved with a hidden input field)
  const input = document.createElement('input');
  document.body.appendChild(input);
  input.value = textToCopy;

  // 2) Select the text
  input.focus();
  input.select();

  // 3) Copy text to clipboard
  const isSuccessful = document.execCommand('copy');

  // 4) Catch errors
  if (!isSuccessful) {
    console.error('Failed to copy text.');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 那么有什么蹊跷呢？

嗯，`navigator.clipboard`目前只有 Chrome 版本😢浏览器完全支持这个剪贴板 API 可能需要一段时间。允许对用户的剪贴板进行读写访问会带来很大的安全问题，浏览器供应商需要时间来做好这件事。

所以现在，我们被`document.execCommand`困住了。幸运的是，有像 [clipboard.js](https://alligator.io/js/clipboardjs/) 这样的库可以省去我们的麻烦。

# 更多信息和代码笔

如果你想了解更多关于使用普通 JavaScript 访问剪贴板的信息，我推荐你阅读来自 alligator.io 的这篇[信息丰富的文章。](https://alligator.io/js/copying-to-clipboard/)

受那个帖子的启发，我还制作了一个[代码笔](https://codepen.io/mornir0/pen/LgLPjo)，展示了一个使用新`navigator.clipboard`的完整示例。