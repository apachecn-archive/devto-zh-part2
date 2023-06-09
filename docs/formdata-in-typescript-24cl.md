# TypeScript 中的 FormData

> 原文：<https://dev.to/deciduously/formdata-in-typescript-24cl>

我正努力养成写一篇文章的习惯，关于任何花费我 15 分钟以上的尝试和错误和谷歌来做正确的事情。有可能我不是第一个也不是最后一个遇到特殊障碍的人。

这既是教程，也是求助！

*编辑* : [@foresthoffman](https://dev.to/foresthoffman) 提供了我需要的帮助！我已经更新了帖子，包括修复。

我正在开发一个小的玩具应用程序，它需要一些控件来指定如何在画布上绘制一些东西。A `<form>`似乎是这种情况下的自然选择，但是使用 TypeScript 中的数据证明有点棘手。

[`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 上的 MDN 页面建议使用如下语法:

```
const form = document.querySelector('form')
const data = new FormData(form);

for (const pair of data) {
    // cool stuff
}
// OR
for (const pair of data.entries()) {
    // cool stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

我没有这些运气。使用`for..of`导致 TypeScript 抱怨`data`不是迭代器(我认为[是](https://developer.mozilla.org/en-US/docs/Web/API/FormData/entries)，或者至少在 JS 中可以像用`for...of`一样使用)，或者`data`没有这个属性`entries`。这更有意义一点——并不是在所有环境中都是如此。我试着将我的`tsconfig.json`调整到目标`esnext`，但是没有成功，无论如何我宁愿保持设置为`es5`。切换到在`data`上使用`for..in`确实如您所料——它列举了`data` :
上所有可用的方法

```
const data = new FormData(form);
for (const entry in data) {
  console.log(entry);
}
/*
get
getAll
has
set
entries
keys 
values
forEach
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我想，还不错，但不是我想要的！`entries`令人沮丧地出现了。为什么我不能用？

这个问题的解决方法很微妙——你需要通过在你的`tsconfig.json`中添加`dom.iterable`来明确告诉 TypeScript 你将使用这个方法——它不会自动随“DOM”:
一起引入

```
"lib":  [  "dom",  "dom.iterable",  "esnext"  ], 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以尽情享受了！尽管如此，这仍然不够简洁——在 JavaScript 中，你可以只写`(let entry of data)`。我们可以通过在`tsconfig.json` :
中增加一行来允许在 TypeScript 中使用这种模式

```
"downlevelIteration":  true, 
```

Enter fullscreen mode Exit fullscreen mode

此编译器选项“在以‘ES5’或‘ES3’为目标时，为‘for-of’、spread 和 destructuring 中的 iterables 提供完全支持。”现在我们的代码可以完全匹配 JS 了！

我将我的变通方法留给后人，因为在一些简单的情况下，我宁愿跳过迭代器，也要这样做。它根本不迭代，它寻找它需要的东西。作为一个例子，下面是问题中的表单的一部分:

```
<form>
        <fieldset>
            <legend>Choices</legend>

            <input type="radio" name="choice" id="choice1" value="choice1" checked>
            <label for="choice1">Choice 1</label>

            <input type="radio" name="choice" id="choice2" value="choice2">
            <label for="choice2">Choice 2</label>
        </fieldset>
        <button type="submit">Do The Thing!</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

为了得到用户的选择，我使用这样的代码:

```
const form = document.querySelector('form')!;

form.onsubmit = (_) => {
  const data = new FormData(form);
  const choice = data.get('choice') as string;
  doCoolThing(choice);
  return false; // prevent reload
}; 
```

Enter fullscreen mode Exit fullscreen mode

一些需要注意的事情——我在`querySelector`调用的末尾使用了`!`操作符。这是非空断言操作符- `querySelector`返回一个类型为`Element | null`的值。我更喜欢让`strictNullChecks`开着，所以 TS 不喜欢我试图对`form`进行操作，就好像它是一个元素一样——这个操作符告诉编译器*我*保证会有一个表单可供选择，并且不会返回 null。

另外，`FormData.get()`返回一个类型为`string | File | null`的值。这是另一个例子，我真的自己写了这个表格——我*知道*这将是一个字符串。我正在使用`as`来转换成合适的类型。

最后，我返回 false 来防止页面被重新加载——对画布的重新绘制发生在`doCoolThing`内部，如果页面重新加载，它将和表单数据一起消失！我没有向服务器发送任何东西，只是使用了页面上的本地用户输入。

这确实有用——我可以一次只获取一个我想要的表单数据，而不需要使用迭代器来配置输出。