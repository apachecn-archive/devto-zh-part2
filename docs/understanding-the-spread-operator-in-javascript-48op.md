# 理解 JavaScript 中的 Spread 运算符

> 原文：<https://dev.to/kball/understanding-the-spread-operator-in-javascript-48op>

较新版本的 JavaScript 在表达能力和开发便利性方面对语言进行了巨大的改进，但快速的变化让许多开发人员感到难以跟上。

随着 Wordpress 现在在新的 Gutenberg 编辑器中采用 React 和现代 JavaScript，Wordpress 开发者的大量受众正在被带入这个世界，不管喜欢与否，并且正在快速追赶。

在这篇文章中，我们将分解 JavaScript 语言最流行的新特性之一 Spread 操作符(又名`...`操作符)。

一个朋友最近请求帮助理解古腾堡区块库的一些示例代码，特别是图库。写这篇文章的时候，这段代码可以在这里看到，但是它已经移动了几次，所以我把它复制在下面:

```
setImageAttributes( index, attributes ) {
  const { attributes: { images }, setAttributes } = this.props;
  if ( ! images[index] ) {
    return;
  }
  setAttributes( {
    images: [
      ...images.slice( 0, index ),
      {
        ...images[index],
        ...attributes,
      },
      ...images.slice( index + 1 ),
    ],
  } );
} 
```

Enter fullscreen mode Exit fullscreen mode

特别是，令人困惑的部分是:

```
images: [
    ...images.slice( 0, index ),
    {
        ...images[index],
        ...attributes,
    },
    ...images.slice( index + 1 ),
], 
```

Enter fullscreen mode Exit fullscreen mode

这看起来确实有点吓人，尤其是如果你最近没有把所有的时间都花在编写现代 JavaScript 代码上。让我们来分析一下发生了什么。

### 数组展开运算符

需要了解的核心部分是`...`语法。这是 spread 操作符，它本质上接受一个数组或一个对象，并将其扩展到它的项目集。这让你可以做一些有趣的事情，例如，如果你有代码:

```
const array = [1, 2];
const array2 = [...array, 3, 4]; 
```

Enter fullscreen mode Exit fullscreen mode

array2 的值最终会是`[1, 2, 3, 4]`。

spread 操作符允许您放入一个数组并获取它的值。

回到我们最初的代码示例，在外层我们有

```
images = [...images.slice(0, index), {some stuff}, ...images.slice(index+1)] 
```

Enter fullscreen mode Exit fullscreen mode

这就是说:将 images 数组设置为从 0 到 index 的旧图像数组，后面跟着一个我们很快会讲到的新数组，后面跟着从 index+1 到结尾的旧图像数组。

换句话说，我们将替换`index`处的项目。

### 为对象展开运算符

接下来，对于扩展语法的对象，您可以执行等同于 Object.assign 的操作，将对象的值复制到新的对象中。请看一个简单的代码示例:

```
const obj1 = {a: 'a', b: 'b'};
const obj2 = {c: 'c', ...obj1}; 
```

Enter fullscreen mode Exit fullscreen mode

这导致`obj2`成为`{a: 'a', b: 'b', c: 'c'}`。

回头看看古腾堡代码的例子，内部级别(在我们对数组的评估中标记为`{some stuff}`)，我们有:

```
{
  ...images[index],
  ...attributes,
} 
```

Enter fullscreen mode Exit fullscreen mode

要翻译:创建一个对象，首先用来自`images[index]`的值填充它，然后用来自`attributes`的值填充它。任何重复的值都会被后面的值覆盖。

所以这就是说:从`index`中取出我的旧图像，将我在`attributes`中的任何值应用于它，并优先使用`attributes`中的值。

如果我们回到整个代码示例:

```
images: [
    ...images.slice( 0, index ),
    {
        ...images[index],
        ...attributes,
    },
    ...images.slice( index + 1 ),
], 
```

Enter fullscreen mode Exit fullscreen mode

整个奇妙的事情就是说:我有一个图像数组、一个索引和一组我想要应用的属性。返回一个新的图像数组，它改变了在`index`的项目，使其具有我的新属性。

### Spread 语法支持紧凑且富有表现力的代码

让我们看看我们已经完成了什么。在一个简短的，现在有希望是可读的语句中，我们成功地创建了一个数组的新副本，该数组在特定的索引处有一个更新的复杂对象。我们没有修改原始数组，这意味着我们代码的其他部分可以调用它，而不用担心副作用。太美了。

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】