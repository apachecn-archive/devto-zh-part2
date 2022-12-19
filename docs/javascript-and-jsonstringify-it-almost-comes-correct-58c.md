# Javascript 和 JSON.stringify:几乎正确

> 原文：<https://dev.to/jacoby/javascript-and-jsonstringify-it-almost-comes-correct-58c>

JavaScript 有 [JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON) ，一个内置的允许你用对象做好的和奇妙的事情。

假设我们创建了一个对象:

```
let obj = {};
obj.a = 1;
obj.b = ["foo", "bar"];
console.log(JSON.stringify(obj)); 
```

我们得到

```
{ "a": 1, "b": ["foo", "bar"] } 
```

这很紧凑，但是我们*可能*想要扩展它以提高可读性。所以，我们去`JSON.stringify(obj,null,2)`。

```
{
  "a": 1,
  "b": ["foo", "bar"]
} 
```

而`JSON.parse`会将文本转换回对象形式。双赢！我喜欢！

但是……为什么那东西会在上面？

因为那是[的替代者](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)。

```
> console.log(JSON.stringify(obj,['b'],2));
{
  "b": [
    "foo",
    "bar"
  ]
} 
```

伙计，这很简单。只是放入一个数组，基本上是说“把事情简化到这个程度。

不得不说，我有点喜欢。

想想你听过的每一部战争电影。被俘时，你给对手姓名、军衔和编号，所以用`JSON.stringify(prisoners,["name","rank","serial_number"])`

你得到一个类似于
的数组版本

```
[{"name":"James Buchanan Barnes","rank":"Sergeant","serial_number":"999-99-9999"},...] 
```

像出生地或激活码这样的乱七八糟的东西搞乱了你漂亮的数据结构。我真的可以看到我的服务器端节点代码对发送到客户端的数据做这些。

只是，我什么都没有。我所有的后端都是 Perl。当我创建对象发送回服务器时，我控制了我要插入的内容，所以我不需要最后一个 catch。

我认为这个问题是一个很大的反模式，**位置参数**。如前所述，stringify 引入了`(value,replacer,space)`，其中**值**是要被字符串化的内容， **replacer** 限制了将要放置的内容， **space** 告诉我们它将如何缩进。并且，按照惯例，你*必须*有值(你*没有*，但是没有它，这有点不可行)，但是你*可以*添加没有空格的替换符，但是你*不能*添加没有替换符的空格，即使替换符是`null`。

另一个选项是**命名参数**，我们可以从`stringify(obj)`或`stringify(value:obj)`开始，更多地依靠 JS 格式，这可能允许我们`stringify(value:obj,space:2)`，所以如果我们不想要它，我们不需要替换。

如果我现在写这个，我会想颠倒位置参数的顺序，因为，嘿，比起过滤的 JSON，我更想要可读的 JSON。但是因为它已经存在，这将打破东西。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。