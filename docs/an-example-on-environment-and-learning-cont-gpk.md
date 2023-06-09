# 一个例子(关于环境和学习竞赛。)

> 原文：<https://dev.to/tttaaannnggg/an-example-on-environment-and-learning-cont-gpk>

所以我正在通过雄辩的 Javascript 工作，我应该用其他函数组成一个函数。好吧。这个概念对我来说确实有意义，我认为我是一个非常专注的研究对象。

但是后来我碰到了这个:

```
function countBy(items, groupName) {
  let counts = [];
  for (let item of items) {
    let name = groupName(item);
    let known = counts.findIndex(c => c.name == name);
    if (known == -1) {
      counts.push({name, count: 1});
    } else {
      counts[known].count++;
    }
  }
  return counts;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是令人沮丧的，所以我写这篇文章主要是作为一种解除自我障碍的方式，并通过这个过程来建立对代码的理解。

我可以看出这是一个名为 countBy 的函数，带有参数“items”和“groupName”。我知道它初始化了一个名为“counts”的空数组。

我开始遇到麻烦的地方是 for/of 循环。“item”在代码中没有任何定义，在文本的其他地方也没有向我介绍过它。

嗯，那很好。我们应该知道如何谷歌东西，对不对？我先从搜索“for of loop js”开始。

这使我们在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 着陆。太好了，现在我可以弄清楚这个循环是如何工作的了，并且可以上路了！

让我们看看页面上的内容:

> 森林...of 语句创建迭代可迭代对象的循环，包括:内置字符串、数组、类似数组的对象(例如，参数或节点列表)、TypedArray、Map、Set 和用户定义的可迭代对象。它调用一个定制的迭代钩子，为对象的每个不同的属性值执行语句。

所以循环遍历可迭代的对象。这就引出了几个问题:

1.  你说的“迭代”是什么意思？
2.  什么是可迭代对象？

我猜想问题 1 的答案是，这类似于我们通过以下代码来访问数组中的每一项:

```
for (let i = 0; i < array.length; i++){
    array[i];
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，目前还不错。然而，我需要的是细节。我知道如何从数组中取出当前项，但是这个东西把我们迭代的对象的名字放在哪里呢？它把与这些名字相关的值放在哪里呢？

我会再看一遍文件来弄清楚。

```
function* foo(){
  yield 1;
  yield 2;
}

for (let o of foo()) {
  console.log(o);
  // expected output: 1

  break; // closes iterator, triggers return
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我可以看到(foo()的 let o)和 console.log(o)，所以在我看来“o”类似于我前面的 for 循环中的“I”。然而,“o”对应的是什么？是位置/名称还是我正在循环的任何东西的值？

预期输出为 1。这似乎对应于我在函数中看到的第一个原始值。产量 1。但是我不知道“yield”实际上是做什么的，所以现在我必须去查一下。幸运的是 [MDN 也有一个关于这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)的页面。

> yield 关键字用于暂停和恢复发生器功能(function*或传统发生器功能)。

好吧，他妈的。

我不知道什么是生成器函数，也不知道什么是传统的生成器函数。

现在，我的选择是要么深入研究什么是生成器函数，要么返回到 for/of 页面，尝试找到一个不同的示例。

显然，让我们做后者。没必要继续深入挖掘自己。

```
let iterable = [10, 20, 30];

for (let value of iterable) {
  console.log(value);
}
// 10
// 20
// 30 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这样好多了。看起来，在这个 for/of 循环中，我们没有处理索引或 w/e。我们只是在每个循环中通过“value”获得数组中每一项的值。我很确定我能应付这个。

但是我确实想谈谈这个文档工作的一部分。没有明确说明被注释掉的位是应该记录到控制台的。

```
// 10
// 20
// 30 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这不是问题。我已经做了足够多的 js，我猜想那可能是什么，我可以自己测试它，以验证我是正确的，因为我知道如何使用 JS 控制台/网站，如 replit，它会给你一个沙箱。然而，如果你在大约一个月前把我放在这些文档前面，我可能无法凭直觉理解这些信息，或者知道如何测试它们。

这就是我在[之前的帖子](https://dev.to/tttaaannnggg/on-environment-and-learning-55e0)中所说的。我不知道作为一个完全的新手，你实际上*会去哪里*获取这些信息，除了问比你更有经验的人。像这样的小痛点有一百万个，当你开始时，它们感觉完全是任意的和意想不到的。如果你知道在哪里/向谁/如何询问，简单的事情可以在几分钟内解决，但是如果你没有这种途径，绝对会粉碎你的动力和动机。

这就是“只搜索它”或“阅读文件”不够的地方，或者，如果是这样，它严重低估了搜索问题答案所花费的时间和精力，这可能会令人沮丧。

我发现编程真正令人愉快的是某种一致性；假设您对基本操作有所了解，那么您可以逻辑地构建您需要的任何类型的功能。当这个过程似乎失败了，或者被我所缺乏的任意的一点知识或惯例所阻碍的时候，我会非常痛苦地感到沮丧。

现在，在完成所有这些之后，我仍然需要查看 findIndex 方法是如何工作的，并且希望我的知识和技能处于正确的位置，能够在我花费所有精力之前解析该文档，然后*然后*我可以最终实际完成工作，弄清楚如何将 countBy 输入或输出其他函数(假设我不需要对我尝试编写的其他函数重复这个过程太多次)。

这就是我有眼袋的原因。