# JavaScript 数组方法让你成为更好的开发人员

> 原文：<https://dev.to/paigen11/javascript-array-methods-to-make-you-a-better-developer-2n1e>

[![close up shot of code text editor on computer screen](img/c21c93a6a7cf5ed59f1c3174e7dee351.png "close up shot of code text editor on computer screen")T2】](///static/e1dbd44cb6d69d235f454f9ce3ac642f/0f98f/code.jpg)

## 数组是 JavaScript 中非常有用的数据结构

当我 **[开始我作为 web 开发人员](https://www.paigeniedringhaus.com/blog/how-i-went-from-a-digital-marketer-to-a-software-engineer-in-4-months)** 的新职业时，我可以在 JavaScript 中循环遍历一组对象的事实非常酷。

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

sayHello = (greeting) => {
    for(let i = 0; i < greeting.length; i++){
        console.log(greeting[i]);
    }
};

sayHello(greetings);
// Hello
// Guten tag
// Salaam
// Hola
// Ciao 
```

Enter fullscreen mode Exit fullscreen mode

> 经典的`for`循环总会有它的位置，但是还有许多其他的数组方法正等着在 JavaScript 中使用。在解决问题时，我会不断地将正确的工具用于正确的工作。

现在，在这方面工作了将近两年，我学到了很多东西，包括一些更复杂的操作数组的方法，今天我想和大家分享一下。

在开始之前，我还想补充一点，一般来说，最好不要改变现有的数组或对象。如果您改变了原来的项目，您将面临不可预见的副作用的风险:在代码中引用数组或对象的任何地方，都可以使用改变后的结果。至少可以说，这使得寻找由变异代码引起的错误的根本原因变得更加困难。

因此，我介绍的方法被称为[纯函数](https://en.wikipedia.org/wiki/Pure_function)，因为它们不是改变原始数组，而是制作一个副本，并对该副本进行更改，然后将其作为新数组返回到方法之外的范围。只是在你阅读这篇文章时要记住的一些事情。

### 地图

第一个非常方便的方法是 [`Array.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 。map 方法接收一个数组，并创建一个新数组，其结果是为该数组中的每个元素调用一个函数。这听起来有点复杂，但一旦你看到一些例子就不会太糟糕。

下面是 Array.map()被用于一个字符串数组:

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

const sayHelloWorld = (hellos) => hellos.map(hello => {
    return `${hello} world`;
});

console.log(greetings);
// ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao']

console.log(sayHelloWorld(greetings));

/* [
  'Hello world',
  'Guten tag world',
  'Salaam world',
  'Hola world',
  'Ciao world'
] */ 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`map`循环访问 greetings 数组中的每个元素，并生成一个名为`sayHelloWorld`的新数组，该数组将每个 greeting 加上单词“world”添加到末尾。但是最初的`greetings`数组保持不变，正如您在`console.log(greetings)`行中看到的。

它还可以应用于复杂的对象数组，而不仅仅是简单的数组。

这里是 Array.map()创建一个新的对象数组:

```
const greetingsByCountry = [{language: "English", greeting: "Hello"},
                            {language: "German", greeting: "Guten tag"},
                            {language: "Arabic", greeting: "Salaam"},
                            {language: "Spanish", greeting: "Hola"},
                            {language: "Italian", greeting: "Ciao"}]

const helloByCountry = (hellos) => hellos.map(obj => {
    let newObj = {};
    newObj[obj.language] = obj.greeting;
    return newObj;
});

console.log(greetingsByCountry);
/* [
  { language: 'English', greeting: 'Hello' },
  { language: 'German', greeting: 'Guten tag' },
  { language: 'Arabic', greeting: 'Salaam' },
  { language: 'Spanish', greeting: 'Hola' },
  { language: 'Italian', greeting: 'Ciao' }
] */

// helloByCountry is the new array of reformatted objects
console.log(helloByCountry);
/* [
  { English: 'Hello' },
  { German: 'Guten tag' },
  { Arabic: 'Salaam' },
  { Spanish: 'Hola' },
  { Italian: 'Ciao' }
] */ 
```

Enter fullscreen mode Exit fullscreen mode

这个对象数组`greetingsByCountry`被`map`用来创建一个新的对象数组，其中键是`language`，值是`greeting`。但是对象的原始数组保持不变。`map`非常强大和有用，你会发现自己经常伸手去拿它。

### 滤镜

[`Array.filter()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 是另一种有用的方法。它的工作方式是从数组中所有元素的原始元素创建一个新数组，这些元素通过了由提供的函数实现的测试。让我们来看一个例子。

**看看 Array.filter()是如何工作的:**

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

const filteredGreetings = filterParam => {
    return greetings.filter((hello) => 
    hello.toLowerCase().indexOf(filterParam.toLowerCase()) > -1
    )
};

// greetings is unchanged
console.log(greetings);
// ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao']

// filteredGreetings is a list of strings that contain the letter 'a'
console.log(filteredGreetings('a'));
// ['Guten tag', 'Salaam', 'Hola', 'Ciao'] 
```

Enter fullscreen mode Exit fullscreen mode

函数`filteredGreetings`接受一个查询参数(在本例中是字符串`"a"`),并检查`greetings`的数组以查看该字母是否存在于这些单词中。如果是，它返回字符串中该字母所在的第一个索引，并将该单词添加到新数组`filteredGreeting`，如果它不在字符串中，函数返回-1，并且它不被添加到数组中。

### 减少

[`Array.reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 方法非常强大:它可以以从简单到非常复杂的各种方式使用，大大减少了实现结果所需的代码量。本质上，`reduce`接受一个数组，对所谓的“累加器”(稍后会详细介绍)和数组中的每个元素应用一个函数，*将*减少到一个值。

`Array.reduce()`也可以接受第二个可选的参数，称为“初始值”,它只是函数第一次回调使用的第一个值。如果没有提供，将使用数组中的第一个值。

现在一个累加器，顾名思义，**累加**每次调用函数的回调时函数返回的值。有道理，对吧？如果答案是“不”，这完全没问题，继续读几个例子来帮助澄清事情。

下面是一个使用 Array.reduce()将数组中的数字相加的简单例子。

```
const prices = [5.15, 7.55, 3.82, 10.11, 6.79];

const sum = (nums) => nums.reduce((acc, currentVal) => {
    return acc + currentVal;
});

const sum2 = (nums) => nums.reduce((acc, currentVal, currentIndex) => {
    return acc + currentVal;
}, 10);

// the array of prices is still intact
console.log(prices);
// [5.15, 7.55, 3.82, 10.11, 6.79]

// sum is its own value
console.log(sum(prices));
// 33.42

// sum2 is its own value
console.log(sum2(prices));
// 43.42 
```

Enter fullscreen mode Exit fullscreen mode

在一组价格上使用`Array.reduce()`的两个例子。

这两个函数接受相同的数字数组并运行相同的函数(将累加器添加到正在迭代的当前值中)，但是第二个`reduce`函数*也*接受初始值的第二个参数。

对于`sum`函数，接受`nums`的数组，因为没有初始值作为第二个参数提供给`reduce`，所以函数从索引默认值 1(如果没有指定)开始，并且在每个循环中迭代每个值，将当前索引值添加到累积值中。

使用`sum2`，初始值`val`被作为第二个参数提供给`reduce`，因此函数从传递给回调函数的第一个值开始，然后继续处理数组中的所有其他数字。这意味着在内部函数运行之前，`sum2’`的值从 10 开始，然后在数组的其余部分循环。

最终，`prices`数组保持不变，`sum`和`sum2`也是它们自己的值(数字)。

准备好再举一个例子了吗？这一个涉及超级英雄只是为了好玩。

第二个 Array.reduce()示例计算了复仇者联盟超级英雄的名字在数组中出现的次数。

```
const superHeroes = ['Black Widow', 'Iron Man', 'Hawk Eye', 'Hulk',
 'Thor', 'Iron Man', 'Thor', 'War Machine', 'Captain America'];

const heroCount = heroes => heroes.reduce((allHeroes, hero) => {
    if(hero in allHeroes) {
        allHeroes[hero]++;
    } else {
        allHeroes[hero] = 1;
    } 
    return allHeroes;
}, {});

// the array of Avengers is intact
console.log(superHeroes);
/* [
  'Black Widow',
  'Iron Man',
  'Hawk Eye',
  'Hulk',
  'Thor',
  'Iron Man',
  'Thor',
  'War Machine',
  'Captain America'
] */

// the number of times each Avenger is named is a rand new object
console.log(heroCount(superHeroes));
/* {
  'Black Widow': 1,
  'Iron Man': 2,
  'Hawk Eye': 1,
  Hulk: 1,
  Thor: 2,
  'War Machine': 1,
  'Captain America': 1
} */ 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`superHeroes`数组被迭代，如果一个英雄没有包含在作为第二个参数传递给 reduce 函数的空对象中，它将创建一个对象，该对象的键作为英雄的名称，计数设置为 1。如果英雄的名字已经出现在对象中，它会增加这个名字在字符串中出现的次数，直到整个数组都被计算在内。

这是另一个如何使用`reduce`来计算一个特定单词在数组中被引用的次数的例子。同样的逻辑也可以用在字符串上，只需简单地将字符串按空格分开，然后通过产生的数组进行缩减，并对单词进行计数。

### ForEach

[`Array.forEach()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 方法是 OG `for`循环的更干净、更精简的版本，但它并不总是正确的解决方案。例如，如果你需要在一个数组中循环，直到找到与你试图寻找的参数相匹配的东西，然后结束循环，`forEach()`不是答案。`forEach`将继续下去，直到它通过了数组中的每个元素。句号。不管它发现了什么或没有发现什么。

如果您想在找到某个值时尽早停止循环，像`some`、`every`或`find`这样的数组方法可能更适合您的需要。

`forEach`的另一个大警告是它**不**返回任何东西。**一个`forEach`循环将总是返回一个`undefined`值。**与`map`或`reduce`不同，`forEach`是不可链接的，除了运行你为数组中的每一项定义的函数之外，它什么也不做。一个典型的用例是在链的末端执行副作用。

这里有一个 Array.forEach()示例，我们对一组问候语进行了重复数据删除。

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hello', 'Ciao', 'Hello'];

/* a new empty array to take in the modified strings */
const dedupedGreetings = new Set();

const sayHello = (hellos) => {
    hellos.forEach((hello) => {
        dedupedGreetings.add(hello);
    }
};

// greetings is unchanged
console.log(greetings);
// ['Hello', 'Guten tag', 'Salaam', 'Hello', 'Ciao']

// the new array has the unique greetings
console.log([...dedupedGreetings]);
// ['Hello', 'Guten tag', 'Salaam', 'Ciao'] 
```

Enter fullscreen mode Exit fullscreen mode

`sayHello`函数遍历数组中的每个字符串，并试图将`hello`添加到`dedupedGreetings`集合中。因为`dedupedGreetings`在`forEach`的之外被声明为*，并且我们通过回调函数给它添加值，所以它工作了，但是这是一个副作用的例子。这并不完全等同于一个`for`循环，但在正确的场景中肯定是有用的。*

* * *

## 其他有用的数组方法

如果有一种情况，你只需要知道一个数组是否包含一个值，至少一次，`Array.includes()`、`Array.some()`、`Array.every()`可能是你需要的方法。

### 包括

正如方法名所示， [`Array.includes()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) 确定一个数组是否包含某个元素，并返回一个`true`或`false`布尔值。

如果数组不止一次包含它，它不会指示它，因为它一找到第一个计算结果为`true`的值就会停止迭代。`includes`也可以把第二个参数作为实参:一个搜索索引，根据索引指定数组的搜索应该从哪里开始。

> 有趣的事实:`includes`如此命名是因为 MooTools 已经在数组原型中添加了一个名为`Array.contains`的方法，它的工作方式与`includes`略有不同。
> 
> 为了避免破坏所有目前使用 MooTools 的`contains`方法的站点，Ecma International 决定将它们的数组方法重命名为`includes`。

**下面是一个简单的 Array.includes()工作的例子:**

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

const includesHello = (hellos) => {
    return hellos.includes('Hello');
}

// greetings is unchanged
console.log(greetings);
// ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao']

// returns `true` because `Hello` is in the greetings array
includesHello(greetings);
// true 
```

Enter fullscreen mode Exit fullscreen mode

这个函数只是在数组中循环，如果字符串包含在数组中的任何地方，就返回`true`。

### 有的

[`Array.some()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 与`includes`类似，但是它不仅仅接受一个数组，它同时接受一个数组以及用来计算数组中各项的值或函数。

`some`将遍历一个数组，并根据是否找到了想要的东西返回`true`或`false`。同样和`includes`一样，`some`会在找到一个评估为`true`的值时立即结束迭代。

**看看这个 Array.some():**
的例子

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

const someHello = (hellos, typeOfHello) => {
    return hellos.some(hello => hello === typeOfHello);
}

// greetings is unchanged
console.log(greetings);
// ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

// the string `Hola` is present in the greetings array, so this function returns true
someHello(greetings, 'Hola');
// true 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，函数`someHello`有两个参数:数组和函数寻找的值(`typeOfHello`)。由于`‘Hola’`存在，函数返回`true`。当你只需要知道一个值是否出现在数组中的时候，`some`可能就是你需要的函数。

### 每

[`Array.every()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 和`some`一样，只有*数组中的每个元素*都必须通过测试并求值到`true`，函数才能在最后返回`true`。这是不言自明的。

下面是 Array.every()的一个例子。

```
const greetings = ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

const everyHelloGreaterThanFour = (hellos) => {
    return hellos.every(hello => hello.length >= 4);
}

const everyHelloGreaterThanSix = (hellos) => {
    return hellos.every(hello => hello.length >= 6);
}

// greetings is unchanged
console.log(greetings)
// ['Hello', 'Guten tag', 'Salaam', 'Hola', 'Ciao'];

// each greeting string has a length of 4 or more chars
everyHelloGreaterThanFour(greetings);
// true

// each greeting string is not 6 or more chars
everyHelloGreaterThanSiz(greetings);
// false 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数`everyHelloGreaterThanFour`，检查`greetings`数组中所有字符串的长度，因为它们都大于或等于 4，所以它返回`true`。

第二个函数`everyHelloGreaterThanSix`，对`greetings`数组做同样的检查，但是长度大于或等于 6，并且它返回`false`，因为不是所有的字符串都至少是那个长度。

### 排序

[`Array.sort()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) 是一个非常有用的方法，但是要记住*如果没有提供比较函数*如何排序也很棘手，比较函数会指导`sort`如何比较和排列数据。

任其自生自灭，`sort`会将所有元素转换为字符串，并根据其 Unicode 顺序比较每个元素(因此，尽管作为字符串，“Banana”在“cherry”之前，但在数字排序中，9 在 80 之前，因为数字被转换为字符串，所以在 Unicode 顺序中，“80”在“9”之前)。

我们通过传递简单的函数来告诉`sort`如何处理遇到的值来解决这个问题。

不过一般来说，这是我经常使用的一个非常强大和有用的方法，我只是倾向于提供一个比较器函数(当我懒惰和不这样做的时候，我已经被咬过很多次了)。

> **注意:**这个方法*确实*修改了原始数组，但是你只是对它进行排序，并没有直接改变数据，所以这种类型的改变对其他代码块产生负面影响的可能性很小。
> 
> 但是，如果您试图对一个足够大的 comples 对象数组进行排序，那么计算量会很大。

下面是 Array.sort()对一个数字数组和一个对象数组的排序。

```
const nums = [86, 10, 47, 29, 6, 55, 110];

// we tell the nums array how to sort its contents from lowest to highest
nums.sort((a, b) => a - b);

// nums is sorted, but otherwise unchanged
console.log(nums);
// [6, 10, 29, 47, 55, 86, 110]

const greetingsByCountry = [{language: "English", greeting: "Hello"},
                            {language: "German", greeting: "Guten tag"},
                            {language: "Arabic", greeting: "Salaam"},
                            {language: "Spanish", greeting: "Hola"},
                            {language: "Italian", greeting: "Ciao"}]

greetingsByCountry.sort((a, b) => {
    let langA = a.language.toUpperCase();
    let langB = b.language.toUpperCase();
    if(langA > langB){
        return 1;
    }
    if(langA < langB){
        return -1;
    }
    if(langA === langB){
        return 0;
    }
})

// now greetingsByCountry is sorted alphabetically by language
console.log(greetingsByCountry);
/* [
  { language: 'Arabic', greeting: 'Salaam' },
  { language: 'English', greeting: 'Hello' },
  { language: 'German', greeting: 'Guten tag' },
  { language: 'Italian', greeting: 'Ciao' },
  { language: 'Spanish', greeting: 'Hola' }
] */ 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数将数字数组从低到高排序。第二个函数根据语言值对各个国家的问候进行排序，首先将所有字符转换为大写(小写也可以)，以消除由大写或小写字母引起的 Unicode 差异。

另一件值得注意的事情是，许多这些方法可以链接在一起，消除了对额外变量的需要，并使您的代码更加整洁。

仅此而已。JavaScript 数组及其内置方法。

* * *

## 结论

这些是我在 JavaScript 中处理数组时最常用的一些方法。如果你很好地掌握了这些，你将能够立刻操纵代码完全按照你想要的去做。

感谢阅读！过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

* * *

## 进一步引用&资源

*   [`Array.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 的 MDN 文档
*   [`Array.filter()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 的 MDN 文档
*   [`Array.reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 的 MDN 文档
*   [`Array.forEach()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 的 MDN 文档
*   [`Array.includes()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) 的 MDN 文档
*   [`Array.some()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 的 MDN 文档
*   [`Array.every()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 的 MDN 文档
*   [`Array.sort()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) 的 MDN 文档