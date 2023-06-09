# Chrome 开发工具中的 5 个有用特性

> 原文：<https://dev.to/codevault/5-helpful-features-in-chromes-devtools-1onk>

Chrome 的 DevTools(和它的 Firefox 对应物)无疑是最常用的调试平台之一，由于大多数开发人员都在使用它，我想列出一些节省时间的功能，我希望我开始时就知道这些功能。

## [T3)1)`$0`到`$4`](#1-raw-0-endraw-through-raw-4-endraw-)

这个非常简单的特性就像是在**元素**选项卡中所选元素的历史记录。其中，如果您在集成控制台内使用它，您可以快速直接地访问您已选择的当前元素，直到您已选择的倒数第五个元素。

作为一个例子，我用它来获取 AngularJS 中所选 DOM 元素的范围，如下:

```
$($0).scope(); 
```

## 2) `$_`

有没有觉得把最后一个执行的语句的结果保存在一个变量中以便在下一个变量中使用是一件很麻烦的事情？不要害怕！使用`$_`你可以得到最后执行的语句的**结果**，而不需要任何复制和粘贴。

我们来看一个例子:

```
getDiamonds('some function', 'with many long arguments');

for (let diam of $_) { console.log(diam.name); } 
```

## 3) `clear()`

有没有想过通过编程来清除控制台，而不仅仅是通过*清除控制台*按钮？有了这个函数，你可以做到这一点，并让控制台返回到它的清除状态，一旦你刷新你的应用程序的一部分。

## 4) `monitor()`

如果你有一个函数被调用了很多次(比如一个事件处理程序),并且你不能 100%确定它传递的参数是正确的。给它添加一个监视器将有助于快速识别。

```
monitor(getDialogue);
getDialogue('main');
console out: "function getDialogue called with arguments: main" 
```

## 5) `$()`和`$$()`

CSS 选择器在控制台中本地实现。简单明了。想在主 div 中选择一个按钮吗？

```
$('.main .btn-submit'); 
```

或者你想得到一个列表中的所有链接？没问题，选择器会掩护你的！

```
$$('ul.links a'); 
```

除了`monitor`函数之外，我们在这里讨论的所有其他特性在 Firefox 调试器中都可以使用。

更多细节请看我制作的这个视频，它更深入地介绍了这些问题:
[https://www.youtube.com/embed/DkIYsjYdz60](https://www.youtube.com/embed/DkIYsjYdz60)

感谢您的阅读，我希望这对你们有些人有所帮助！