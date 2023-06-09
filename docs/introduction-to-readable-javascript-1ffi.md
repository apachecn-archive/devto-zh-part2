# 如何用 JavaScript 编写可读的代码

> 原文：<https://dev.to/canderson93/introduction-to-readable-javascript-1ffi>

偶尔会有一篇文章列出了一些关于如何保持代码可读性的规则。每个人都希望他们的代码是自文档化的，但是似乎没有人同意如何去做。

如果你正在阅读这篇文章，你可能知道你的代码不是最干净的。如果你的代码能正常工作，那么代码有点乱有什么关系吗？

确实如此。真的是这样。

可读代码减少了[认知负荷](https://en.wikipedia.org/wiki/Cognitive_load)。这使得使用它更加容易和快速。

学习编写自我文档化的代码并不是为了成为一名更好的开发人员，而是为了成为一名更好的队友。

为了帮助你编写可读的代码，我建立了一些准则。这些绝不是代码可读性的全部。相反，它们是你开始和扩展的基础。

## 使用样式向导

提高代码可读性的最简单、最直接的方法就是使用样式指南。

风格不一致的代码就像试图阅读从杂志剪报中拼凑出来的绑匪的纸条。

你*能*看懂，但肯定更难。

如果你还没有使用风格指南，我建议你选择一个公共的，比如 AirBnB 风格指南。编写这些代码是为了提高可读性，因此您应该会注意到代码中的即时改进。

不过，风格指南的具体规则并不那么重要。更重要的是你要始终如一。请随意修改您的风格指南，以适合您和您的团队。

## 使用明确的名称

关于以下功能，你能说些什么？

*   `List.a()`
*   `List.add()`
*   `List.addItem()`

有一点是肯定的——名字越长，就越清晰。

我不知道为什么，但是当我开始的时候，我有一个关于不写长函数和变量名的想法。为了简洁起见，我会缩写或使用模糊的描述符。

我现在就要让你免受一些痛苦。长的变量名比不清楚的变量名好。

当你两周后回来时，你不会记得那个`a`函数或变量是做什么的。

## 使用大量小型命名函数

函数名是代码可读性的隐藏宝石之一。除了防止代码重复，函数还是讲述故事的有力工具。看看这个。

```
const handleClick = function updateTitleOnClick(event) {
    event.preventDefault();
    const titleKey = event.target.getAttribute('data-new-title');
    titleService.getTitle(titleKey).then((title) => {
        document.querySelector('#title').innerText = title;
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

肯定有可能知道发生了什么，但是如果我们把事情分解成小函数，并给我们的匿名函数命名呢？

```
const handleClick = function updateTitleOnClick(event) {
    event.preventDefault();
    getButtonTitle(event.target)
        .then(updateTitle);
}

const getButtonTitle = function getButtonTitle(button) {
    const key = button.getAttribute('data-new-title');
    return titleService.getTitle(key)
}

const updateTitle = function updateTitle(title) {
    document.querySelector('#title').innerText = title;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个干净多了。很明显`handleClick`函数正在采取什么步骤。我们所要做的就是增加两个函数。

## 不要使用神奇的数字

幻数是一个真正的可读性问题。告诉我下面的代码是什么意思:

```
if (items.length > 15) {
    // What even is this?
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为很难判断它在做什么，以及它为什么会在那里。这个 15 代表什么？这个 15 与代码中更下面的 15 相同吗？

相反，比起在代码中直接使用原语，更喜欢使用命名变量。

```
const MAX_CART_SIZE = 15;
if (items.length > MAX_CART_SIZE) {
    // It's pretty clear what this is now
} 
```

Enter fullscreen mode Exit fullscreen mode

## 谨慎使用注释

注释是自记录代码中有争议的一点。纯粹主义者认为，如果你认为你需要注释，你实际上需要重写你的代码，使之更加清晰。

这种对注释的坚决反对是因为糟糕的注释会降低代码的可读性，同时不提供任何价值:

```
// Set the numerator and denominator
const numerator = 5;
const denominator = 4;

// Calculate the division and round it
const result = 5 / 4;
result = Math.round(result); 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，再多干净的代码也不能代替一个合适的注释或真实的文档。

评论的难度足以保证一篇属于自己的文章。要点是你想让你的注释捕捉那些你*无法从代码中分辨出来的信息。* 

```
function example() {
    // We had to use doFunc() here instead of setFunc() because setFunc()
    // isn't compatible with someOtherFunc()
    return doFunc();
} 
```

Enter fullscreen mode Exit fullscreen mode

很难判断什么时候这种评论是正当的。有疑问的时候，就留下评论吧。留下多余的信息比漏掉重要的东西要好。

## 记住这一切都是主观的

代码可读性如此难以正确理解的原因之一是每个人都不一样。10 年的行业老手不会像 3 周前的人一样阅读代码。

没有人会像你一样使用你自己的代码，所以写代码要符合你的标准。

现在你已经完成了这篇文章，回到你的一个旧项目，清理它。你会惊奇地发现这有多么不同。

* * *

你还停留在 JavaScript 教程上吗？

下载我的电子书，它讲述了如何成为一名成功的自学 JavaScript 开发者，以及如何找到你将真正完成的项目。