# 深层拷贝和不变性问题

> 原文：<https://dev.to/samdbeckham/deep-copy-and-the-immutability-issue--cd6>

在最新一集“[我不知道我在做什么](https://www.youtube.com/watch?v=v6FQrXHv2h8&index=7&list=PLQnVLZV0MsRKVTJvKDTFsyWYRLn499JP6)”中，我了解到我所认为的关于 Javascript 中不变性的一切都是谎言。

好吧，我太夸张了。并非一切都是谎言。但是我理解的一个基本部分是不正确的。在与一些人谈论过这个问题后，这似乎是一个共同的误解。

这一切都源于我们在 javascript 中复制对象的方式有一个微妙但基本的不同。深层复制和浅层复制。

深度复制是我们想要的真正的不可变数据。它是一个对象的所有值以及其中所有对象的所有值的副本。另一方面，浅层复制是一个对象的所有值的副本，引用其中的所有对象。这就是我犯错的原因。

为了理解这个问题，我们需要看一下**复制对象的三种方式**。

## 引用

好吧，让我们把这个一路剥开。让我们创建一个对象的可变引用。

```
const initialObject = { name: "Sam", twitter: "@samdbeckham" };
const newObject = initialObject; 
```

这对不变性是不利的，因为对`newObject`的任何改变都会反映在`initialObject`中，就像这样:

```
newObject.twitter = "@frontendne";
console.log(initialObject.twitter); // @frontendne 
```

在本例中，`newObject`是对`initialObject`的引用。因此，每当我们在这些对象中的任何一个上获取或设置数据时，它也会应用于另一个对象。这在许多不同的方面都很有用，但对于不变性来说不是很好。

## 浅薄的抄袭

这是以不可变方式复制数据的最常见形式。我们利用 spread 操作符来创建一个`initialObject`的副本。如果你以前用过 redux，你会在你的 reducers 里面看到这个。

```
const initialObject = { name: "Sam", twitter: "@samdbeckham" };
const newObject = { ...initialObject }; 
```

这是一个微妙的变化，但`...`让一切变得不同。`newObject`不再与`initialObject`关联。它现在是数据的副本和一个全新的对象。因此，如果我们进行与之前相同的更改，我们会得到以下结果:

```
newObject.twitter = "@frontendne";
console.log(initialObject.twitter); // @samdbeckham
console.log(newObject.twitter); // @frontendne 
```

修改`newObject`上的数据不再影响`initialObject`。我们可以继续我们的一天，修改`newObject`和`initialObject`保持干净。

但这是浅层的复制，不变性只有一层深。为了展示这一点，我们需要在我们的`initialObject` :
中有一个对象

```
const initialObject = {
  name: "Sam",
  social: {
    twitter: "@samdbeckham",
    youtube: "frontendne"
  }
};
const newObject = { ...initialObject }; 
```

乍一看，这个`newObject`看起来像是`initialObject`的不可变副本，但是看看当我们这样做时会发生什么:

```
newObject.social.twitter = "@frontendne";

console.log(initialObject.social.twitter); // @frontendne 
```

可悲的是，不变性只是表面现象。一旦我们进入下一个层次，我们又回到引用值。如果我们打开`newObject`，它看起来有点像这样:

```
const newObject = {
  name: "Sam",
  social: initialObject.social
}; 
```

我们可以通过浅复制更深一层并像这样定义`newObject`来解决这个问题:

```
const newObject = {
  ...initialObject,
  social: { ...initialObject.social }
}; 
```

在 redux 中通常是这样处理的，但这只是增加了一层不变性。如果有任何其他嵌套对象，它们仍将被存储为引用。您可以看到(对于某些数据结构)这会变得多么混乱。

***注:**`Object.assign()``Object.freeze()`和 spread 一样有浅层复制问题。*

## 深度复制

最后，我们到了深度复制。深度复制为我们提供了真正的对象不变性。我们可以改变对象中的任何值——不管它的嵌套有多深——并且它不会改变我们从中复制的数据。

```
const initialObject = {
  name: "Sam",
  social: {
    twitter: "@samdbeckham",
    youtube: "frontendne"
  }
};
const newObject = deepCopy(initialObject);

newObject.social.twitter = "@frontendne";

console.log(initialObject.social.twitter); // @samdbeckham
console.log(newObject.social.twitter); // @frontendne 
```

万岁！我们是不可改变的！

不幸的是，Javascript 没有名为`deepCopy()`的函数，所以我们不得不自己做一个；它不漂亮。在 Javascript 中没有处理深度复制的“好”方法。达斯·苏尔马写了一篇关于深度复制的文章，其中有一些很好的例子，这里是一些更简单的例子。

### JSON

这是最简洁易懂的方法，看起来是这样的:

```
const deepCopy = object => JSON.parse(JSON.stringify(object)); 
```

首先，我们用`JSON.stringify()`将对象转换成 JSON 字符串，然后用`JSON.parse()`将该字符串转换回对象。将数据字符串化会抛出所有引用，使得返回的对象完全不可变。但是，如果我们需要在这个对象中保存任何引用，它们都不见了。如果我们有任何地图、正则表达式、日期或其他特殊类型；他们走了。如果我们在对象内部有任何循环对象(这是我们不应该的)，整个程序就会中断并抛出[错误](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Errors/Cyclic_object_value)。所以不是很健壮。

### 数据洗钱

如果您不想处理 JSON 解析器带来的问题，您可以使用一些方法——尽管有些粗糙。这些都围绕着将我们的数据传递给一个服务，然后查询该服务以将我们清理的数据提取出来。这就像洗钱，只不过用的是数据，而且一点也不酷。

例如，我们可以利用通知 API:

```
const deepCopy = object =>
  new Notification("", {
    data: object,
    silent: true
  }).data; 
```

这将触发一个通知，使其静音，然后从该通知返回数据。不幸的是，用户需要能够接收通知才能工作。

我们也可以以类似的方式利用历史 API 和`messageChannel`。但是它们都有缺点。

## 现在做什么？

深度复制对不变性来说有点粗暴。意识到浅层复制的缺陷应该足以帮你解决大多数问题。您可以使用上面概述的嵌套展开方法来修复任何问题区域。如果这种方法开始变得笨拙，你应该首先改进你的数据结构。

如果你绝对**需要**深度复制，那么不用担心。HTML 规范中有一个[问题，希望通过`structuredClone()`的引入来解决这个问题。这一点越明显，就越有可能实现。在那之前，我建议使用像](https://github.com/whatwg/html/issues/793) [Immutable.js](https://facebook.github.io/immutable-js/) 这样的库来处理你的不变性。或者从[下划线](https://lodash.com/docs/4.17.5#cloneDeep)库中抓取`cloneDeep()`助手进行快速修复。

如果你准备迎接挑战，试着为 deepCopy 想出自己的解决方案。我的朋友 Niall 在 Twitter 上玩一些想法玩得很开心。我很有兴趣看看你们都提出了什么。

## [https://www.youtube.com/embed/v6FQrXHv2h8](https://www.youtube.com/embed/v6FQrXHv2h8)T3】

这篇文章最初发表在[我的网站](https://sam.beckham.io/wrote/deep-copying-and-the-immutability-issue.html)