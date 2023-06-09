# 如果(！_ 如果)什么

> 原文：<https://dev.to/mahlongumbs/if-if-what-1jda>

有时，`if`语句的使用会在我的计算圈子里引起一点争论(*听到我们以“如果你使用 if…”开始争论很有趣*)。最近，我偶然发现了这个[帖子](https://dev.to/genta/theres-no-else-if-in-js--24f9)。在其中一条评论中，断言应该避免使用`if`语句，因为它们代表了设计缺陷。虽然我不同意代码中存在`if`语句是不好的，但我受到启发分享了一些我倾向于避免使用它们的例子。本文主要关注 JavaScript，但是提出的大多数概念都是与语言无关的。

### 众说纷纭的例子

在前述文章的评论中，我们中的许多人开始重写下面的示例`if...else`块。

```
const wow = arg => {

  if(arg === "dog"){
    return "LOVELY";
  } else if(arg === "cat"){
    return "CUTE";
  } else {
    return ("gimme an animal");
  }
}

wow("cat");
//-> "CUTE" 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个例子很好地证明了作者的观点(*我们还是把它拆开了，因为除了我们的薪水支票*之外，我们会把所有东西都拆开)，但它确实提供了一些改进的机会。

### 否则如果，否则如果，否则如果

第一个问题是，每当需要一个新条件时，必须添加一个新的`else if`子句。所以如果你想说“真棒”来回应“小马”，你需要调整代码如下:

```
const wow = arg => {

  if(arg === "dog"){
    return "LOVELY";
  } else if(arg === "cat"){
    return "CUTE";
  } else if(arg === "pony"){
    return "AWESOME";
  } else {
    return ("gimme an animal");
  }
}

wow("pony");
//-> "AWESOME" 
```

Enter fullscreen mode Exit fullscreen mode

这将对每一个新的动物重复，并且使得代码非常脆弱，难以测试。

### 条件句

与其使用这么多的`if...else if`块，不如用条件语句重写函数。下面是一篇链接文章的评论，展示了这种方法:

```
const wow = arg => (
  (arg === "dog" && "LOVELY") ||
  (arg === "cat" && "CUTE") ||
  "gimme an animal"
);

wow("cat"); 
```

Enter fullscreen mode Exit fullscreen mode

没有出现`if`陈述，但是您仍然会遇到最初的维护问题。也就是说，您需要为每个新动物添加一个附加条件。

### 资料图

消除这些不断增长的`else if`语句的一个方法是将你的关系存储在一个地图中。考虑以下情况:

```
const animals = {
  dog: "LOVELY",
  cat: "CUTE",
  pony: "AWESOME",
};

const wow = arg => {
  return animals.hasOwnProperty(arg) && animals[arg] || "gimme an animal";
};

wow("pony");
//-> "AWESOME" 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们用数据图中的查找代替了`if...else`语句。这样，我们大大简化了`wow`函数，当新动物出现时，我们不再需要修改它。

在继续之前，我想指出删除`if`语句是*而不是*这里的重点。重点是让你的代码不那么脆弱，更容易维护。这个例子的最新迭代也可以写成如下形式:

```
const animals = {
  dog: "LOVELY",
  cat: "CUTE",
  pony: "AWESOME",
};

const wow = arg => {
  if(animals.hasOwnProperty(arg)){ //WTF if, who invited you?
    return animals[arg];
  }
  return "gimme an animal";
};

wow("pony");
//-> "AWESOME" 
```

Enter fullscreen mode Exit fullscreen mode

### 更进一步...

您可能会看着上面的内容，然后宣布“但是我仍然必须更改代码！有什么区别？”我不会为此责备你。因此，在这一节中，我将做一点调整，以便把这一点讲清楚。

首先，让我们把数据抽象出来。

```
//file: data.js

let animals;

//Let's pretend this is really being loaded from the database
//Let's also pretend the load is synchronous so we don't have
//get into a discussion of async/await or the Promise api
const loadAnimals = () => {  
  animals = {
    dog: "LOVELY",
    cat: "CUTE",
    pony: "AWESOME",
  };
};

const getAnimals = () => {
  if(!animals) loadAnimals();
  return animals;
};

export default getAnimals; 
```

Enter fullscreen mode Exit fullscreen mode

在本模块中，我们将模拟一个数据库。public `getAnimals`方法将从我们的数据源返回数据。记住，整个`animals`结构存在于数据库中，所以对它的修改会发生在那里，而不是在这个文件中。为了这个讨论，让我们假设`data.js` ***是*** 数据库。

接下来，我们实现我们的`wow`模块。

```
//file: wow.js

import getAnimals from 'data';

const wow = name => {
  const animals = getAnimals();
  return animals.hasOwnProperty(name) && animals[name] || "I'm sorry Dave, I'm afraid I can't do that";
};

export default wow; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这里我们导入了数据模块，并使用它来获取动物结构。然后，和以前一样，如果没有找到与指定名称匹配的动物，我们或者返回问候(如果有)或者返回愚蠢的字符串。

重要的一点是，即使这组动物发生变化或者每个动物的问候语发生变化，这个模块也不需要修改。这使得它更易于维护，因为修改或添加动物变成了数据输入的问题，而不是编码的变化。您的单元测试被极大地简化了，因为您不需要为每只动物测试一个分支。事实上，仅通过下面两个测试，您就可以在这个单元中获得 100%的代码覆盖率。

*   应该接受指定动物的名称并返回问候。
*   如果没有匹配的动物，应该返回`I'm sorry Dave, I'm afraid I can't do that`；因为所有的错误信息听起来都应该像一台听起来像人类的计算机试图听起来像一台听起来像人类的计算机。

最后，您可以从某个地方导入并使用这个模块(这里我们只使用`index.js`)。

```
//file: index.js

import wow from 'wow';

wow('pony'); //-> AWESOME
wow('horse') //-> gimme an animal 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

听着，我不是来告诉任何人如何编码的。我不认为使用`if`语句有什么根本性的错误。我绝对不相信绝对。我敢肯定，最后那句话伤害了锁在那个盒子里的同一只猫薛定谔。他曾经为自己的行为向善待动物组织负责吗？

无论如何，基于你的项目的需要和你说服与你一起工作的编码狂热者睁一只眼闭一只眼的能力，你很可能可以将一些`if...else if...else`语句串在一起并发布出去。但是，有一些替代方法可以增强代码的稳定性和可测试性。这篇文章指出了这个特殊问题的冰山一角。如果有兴趣的话，我会写更多关于这方面的文章，并探索一些其他有用的流行模式。如果没有，就告诉我一半时间去那个家伙的猫在的地方。*地狱。我说的是地狱。*