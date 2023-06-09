# 在网页上加载脚本

> 原文：<https://dev.to/jochemstoel/loading-scripts-on-your-webpage-576c>

我错过了火车，有一个小时的时间，所以让我们来谈谈简单的事情，在网页上加载脚本。

## 成束

强制加载所有脚本的一个超级简单的方法是将它们打包到一个文件中。然而这是昂贵的(愚蠢的),因为你不能通过这种方式只进口你需要的。

## 为脚本文档

 *作为小鸭子，我们被教导将脚本放在文档*头*中，因为它们是在加载网页时立即加载的第一件事。

```
<html>
    <head>
        
        <script src="main.js"></script>
        <script src="util.js"></script>
        <script src="events.js"></script>
        <script src="speech.js"></script>
    </head>
    <body>
    <h1>Page Title</h1>
    </body>
</html> 
```

文档 *head* 中的脚本元素不能访问在脚本之后声明的 HTML 元素，因为当加载脚本时，目标元素还不存在。换句话说，在上面的例子中，你不能从 *main.js* 中访问 *h1* 元素。这就是为什么小鸭子经常希望在页面的其余部分已经加载之后才加载他们的脚本。

## 作为脚本在*正文*的结尾

如果您想在文档加载后执行脚本，那么只需将它们放在主体的末尾。从某种意义上来说，这样做已经成为一种惯例，因为这样可以加快页面的加载速度。这意味着页面图像和样式表已经被加载了。脚本标签不会阻止/延迟它们。这对用户来说更好。

```
<html>
    <head>
        
    </head>
    <body>
    <h1>Page Title</h1>
    <script src="main.js"></script>
    <script src="util.js"></script>
    <script src="events.js"></script>
    <script src="speech.js"></script>
    </body>
</html> 
```

很有可能从 page *head* 中声明的脚本中访问页面上的元素，但是您必须等待一个事件告诉您页面已经加载。过去，人们给页面主体分配了一个 *onload 属性*。

```
<body onload="method()"></body> 
```

## 使用 jQuery

大家都知道下面这些。

```
$(document).ready(function() {
  // the page has finished loading
} 
```

## 香草

这与上面的*几乎*相同，但是没有 jQuery。

```
document.addEventListener('DOMContentLoaded', event => {
  // the page has finished loading
}) 
```

## 程序化地注入它们

你可以通过*将脚本注入你的大脑*来模仿某种需要的功能。这真的没有听起来那么可怕。

```
function importScript(src) {
    let script = document.createElement('script')
    script.setAttribute('src', src)
    document.head.appendChild(script)
}

importScript('main.js')
importScript('util.js')
importScript('events.js')
importScript('speech.js') 
```

## 带异步功能

有些人坚持将多个承诺包装到一个异步函数中。

```
// imagine the same import function but one that implements a Promise.
document.addEventListener('DOMContentLoaded', async event => {
  window.main = await importScript('main.js')
  window.util= await importScript('util.js')
  window.events= await importScript('events.js')
  window.speech = await importScript('speech.js')
} 
```

## 为模块

现在是 2018 年，小鸭子变成了天鹅，我们可以在脚本标签中使用额外的属性来导入模块。函数式程序员热衷于此，他们可能是它蔓延到 Node 的原因。

```
<script type="module">
  import * as util from './util.js'
  util.default()
  util.extra()
</script> 
```

```
<script type="module">
  import { display, extra } from './main.js' // that too
  display.message()
  extra()
</script> 
```

> 我一个朋友问 Node 为什么不*简单的用导出*。这是因为 Node 比 export 关键字出现的时间要早得多。:P

用作函数的 import 关键字使[能够动态导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Import)。它返回一个承诺，该承诺解析脚本导出的任何内容。像这样使用导入*不需要*脚本标签的 *type="module"* 属性。

```
import('./util').then(module => window.util = module).catch(console.error) 
```

因为导入使得[承诺](https://developer.mozilla.org/nl/docs/Web/JavaScript/Reference/Global_Objects/Promise)，只要我们的事件处理函数是异步的，我们就可以在 [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded) 事件中等待它。

```
document.addEventListener('DOMContentLoaded', async event => {
    window.util = await import('./util')
}) 
```

要加载多个脚本，只需迭代一个数组

出于某种原因，您可能希望解析器函数通过标识符(不是完整路径)导入脚本，而不是一个上下文对象(在本例中默认为 window)。你在下面看到的并不理想，但是你明白了。

```
let libs = ['main', 'utils', 'session']
const init = async (context = window) => libs.forEach(async lib => context[lib] = await import(init.resolve(lib)))
init.resolve = lib => `./js/${lib}.js`

init(window) // libs are now properties of window
init({}) // initialize on empty object
init({
    utils: 'abc'
}) // utils is overwritten 
```

## 使用[要求](https://requirejs.org/docs/start.html)

就我个人而言，我从来不明白为什么有人会认为这是他们生活中所需要的。它从来没有为我解决任何问题。然而，因为它的声誉，它需要被包括在内。

```
requirejs(["util"], util => {
    //This function is called when util.js is loaded.
    window.util = util
}) 
```

## 获取

*的[简化](https://github.com/jochemstoel/acquire/blob/master/acquire.js)要求*在其包含模块标识符的上下文中评估 XMLHttpRequest 的 responseText。当时没有 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 。没有模块脚本或导入/导出关键字。Acquire 通过一个函数调用同时支持同步和异步，但是同步 XMLHttpRequest 可能是最不受欢迎的方法。

> 如果你喜欢这篇文章，那么请经常来 dev.to*