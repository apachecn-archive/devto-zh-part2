# 反对开关的理由

> 原文：<https://dev.to/jckuhl/a-case-against-switches-13pd>

## 问题

开关很丑。它们很容易出错。默认的失败行为是请求错误。我认为 Swift 做得对，把`fallthrough`变成了一个关键字，而不是一个默认值，但即便如此，如果可以的话，我还是宁愿避免使用它们。他们只是不符合我的代码的其余部分。缩进很尴尬，似乎没有人能决定 case 语句是否缩进。

Python 甚至懒得用语言来实现它们。

我主要从事 JavaScript 工作，所以我将把重点放在这种语言上。然而，任何具有一级函数和某种键/值对结构的语言都可以避免开关。比如 Java，可以使用 maps 和 lambdas。但是我将坚持使用 JavaScript。

## JavaScript 对象

我们如何避免开关？好吧，switch 中的每个案例本质上都是一个键值对。您将匹配单个键、大小写、单个值、要计算的表达式或一组指令。听起来熟悉吗？归结为两个关键的工作，*键*和*值*，你就有了一个基本的 JavaScript 对象。我们如何使用 JavaScript 对象来代替开关？

让我们从一个例子开始。让我们假设有一些代码在登录失败时显示一条错误消息。

```
errorMessage(error) {
    const status = {
        '401': 'Please check your username and password',
        '404': 'Account not found, have you registered?',
        '500': 'Something went wrong with the server, please try again later',
        'Failed to fetch': 'Servers are currently down, please try again later'
    }
    return status[error.status];
} 
```

这里我们有像开关一样的代码。我们有 4 个不同的错误消息可以抛出，如果验证失败是 401，如果找不到用户是 404，如果出现问题是 500，或者如果服务器关闭是`Failed to fetch`。我们所要做的就是对一个物体进行最基本的查找，仅此而已。无脱落，无震动`switch`结构。只是一个基本的 JavaScript 对象。

但是如果我想要一个违约案例呢？这也很简单，我们只需要检查值是否在对象本身中。有很多方法可以做到这一点，但是我将通过检查`undefined` :
来检查该属性是否存在

```
errorMessage(error) {
    const status = {
        '401': 'Please check your username and password',
        '404': 'Account not found, have you registered?',
        '500': 'Something went wrong with the server, please try again later',
        'Failed to fetch': 'Servers are currently down, please try again later',
        default: 'Something borked, sorry!'
    }
    if(!status[error.status]) {
        return status['default'];
    }
    return status[error.status];
} 
```

当前的 JavaScript 也相当灵活。如果我想使用数字而不是字符串作为对象键，我可以这样做。JavaScript 会在幕后将它们转换成字符串。因此下面的 JavaScript 也是有效的:

```
const object = {
    1: 'one',
    2: 'two',
    3: 'three'
}

object[1]; // 'one' 
```

当然，你不能在这个对象上使用点符号，`object.1`是无效的，但如果我们只是简单地把这个对象作为一个开关，那就没关系了。对于括号符号，点符号不是强制性的。但这里重要的是，我们可以用字符串和数字重新创建开关行为。现在你*可以*使用`true`和`false`作为键的字符串，如果你想生成一个布尔值，但是我认为一个开关对一个布尔值来说是多余的。

## 功能？

然而，当我们使用`switch`时，我们经常做的不仅仅是抓取字符串和数字，我们还可能持有函数。谢天谢地，JavaScript 是一种将函数视为一等公民的语言。函数可以像其他对象一样传递，当然，也可以是对象中的属性值。

在这里，箭头函数确实很出色，尽管如果你需要保留`this`，你必须使用`Function.prototype.bind()`，或者使用 JavaScript 匿名函数的旧语法`function () { ...`。JavaScript 对象中的函数速记也保留了`this`的上下文，在这种情况下，函数的名称成为键，指令块成为它的值。

```
const greet = {
    sayHello1: function() { return 'Hello' },
    sayHello2() { return 'Hello' },
    sayHello3: ()=> { 'Hello' }
} 
```

在这个例子中，`greet.sayHello1()`和`greet.sayHello2()`做完全相同的事情。`greet.sayHello3()`略有不同，因为它是一个箭头函数，因此`this`关键字丢失了。然而，由于这个函数没有使用`this`，所以在这个特定的场景中，这三者是完全相同的。如果你需要`this`来实现箭头功能，你可以做`greet.sayHello3.bind(greet)`。

想象我们有一个基于文本的角色扮演游戏。你扮演一个巫师，他有很多法术可以施展。用户输入他想要的咒语，然后巫师施放它。你可以使用一个开关来决定使用哪个法术，或者使用一个物体:

```
function castSpell(spellname) {
    const spellbook = {
        fireball: ()=> 'Wizard casts Fireball!',
        iceshard: ()=> 'Wizard casts Ice Shard!',
        arcanemissiles: ()=> 'Wizard casts Arcane Missiles',
        polymorph: ()=> 'Wizard casts Polymorph!',
        default: ()=> 'Wizard doesn\'t know that spell.'
    }
    if(!spellbook[spellname]) {
        return spellbook['default']();
    }
    return spellbook[spellname]();
} 
```

这个函数的作用是，你传入一个拼写名称，它使用这个拼写名称来匹配拼写本中的一个值。那个值是一个函数，所以通过使用`()`抓取值后会调用那个函数。

> 为了保持一致，我在这里使用括号符号作为默认方法。`spellbook.default()`也同样有效。

在这里，我们可以像在开关中一样调用函数。你可以抽象出所有的 case 语句代码，把它们放入一个对象方法中，然后简单地通过括号符号调用它们。

这确实有一些权衡，因为比`case 'fireball': return fireball();`更难判断`spellbook[spellname]()`在做什么，但是代码更优雅，缩进层次更少，没有出错的威胁。

## 但是我想失败！

哦。那好吧。获得一个对象的失败行为更加困难，而且没有一种方法可以做到。这里可能会有一个争论，即`switch`实际上可能是一个更好的构造。如果是，那么使用`switch`。但是了解到对象上有许多方法，也有其他的解决方案。使用`Object.values()`、`Object.keys()`和`Object.entries()`，您可以将所有的键/值对放入数组，然后通过任意数量的数组函数运行它们。这样做可以实现故障排除。

想象一下，我们有一个有很多函数的对象，给定一个数，我们需要调用所有的函数，不包括这个数。在这种情况下，切换回退是有用的，但这也很容易用对象来完成。这里有一个例子:

```
function callFns(number) {
    const object = {
        1: ()=> console.log('one'),
        2: ()=> console.log('two'),
        3: ()=> console.log('three'),
        4: ()=> console.log('four'),
        5: ()=> console.log('five')
    }

    Object.keys(object).forEach(key => {
        if(key >= number) {
            object[key]();
        }
    });
} 
```

调用`callFns(3)`，控制台将记录`'three'`、`'four'`、`'five'`。这将模拟使用`switch(3) {`而在任何情况下都没有`break`或`return`。通过结合对象和数组的方法，我们可以为我们的情况模拟一个漏洞。但同样，这也可能是一种`switch`可能是更好的构造的情况。毕竟，开关*出现故障的主要原因是*的漏检特性。但是，通过使用对象，您可以访问许多方法，这些方法可以使对象比 switch 语句更灵活。通过获取对象条目的数组，您可以访问`filter`、`reduce`、`some`、`every`以及类似`map`、`forEach`的迭代方法和类似`for of`的结构。

## 总结

JavaScript 中的对象为您提供了一个简单的替代语句`switch`。对象是灵活的，比`switch`语句更不容易出错，并且在代码中不像 switch 语句那样不和谐。如果你不想失败，用一个对象代替开关是一个更好的选择。如果您确实想要 fallthrough，可以通过对象和数组方法来实现，但是常规的`switch`可能是更好的选择。

总而言之，你的代码风格取决于你自己，但是我建议像 Python 一样，把开关完全扔掉。

快乐编码。