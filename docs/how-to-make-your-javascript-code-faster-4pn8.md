# 如何让你的 Javascript 代码运行得更快

> 原文：<https://dev.to/singuerinc/how-to-make-your-javascript-code-faster-4pn8>

很久以前，在 2011 年，我写了我的第一个 DNI(西班牙 ID) [验证脚本](https://blog.singuerinc.com/javascript/actionscript3/validators/dni/nie/nif/2011/06/17/tip-validar-un-dninienif-de-espana-con-actionscript/)。我是用 ActionScript 做的。

几年后，在 Flash 时代之后，我用 Javascript 重写了它。

最近，我进入了一个功能性很强的编程学习过程，作为一个目标，我建议自己创建一个新的模块来应用我所学到的东西。

结果(我认为不是太遵循 FP 范式)是:

#### 更好的 DNI

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[更好-dni](https://github.com/singuerinc/better-dni)

### 最快的西班牙人 DNI(聂/ NIF)验证出来了。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![npm](img/5495e8aaf1e19c21f6a33c20d5cf0cc2.png)](http://npm.im/better-dni)[![Codacy grade](img/e18356c1fe8cae744b4b53e22817d535.png)](https://app.codacy.com/app/nahuel.scotti/better-dni)[![Build](img/7241c5120702b96c80ac55f55144a015.png)](https://github.com/singuerinc/better-dni/workflows/Node.js%20CI/badge.svg)[![Coveralls github](img/1f875df01c18a8d093c0ad556ad576ed.png)](https://coveralls.io/github/singuerinc/better-dni?branch=master)[![Known Vulnerabilities](img/4c1af32c2055bec161058219324ce762.png)](https://snyk.io/test/github/singuerinc/better-dni)[![npm bundle size (minified)](img/c0481f7c6c007b2d951fc16acb9679a5.png)](https://camo.githubusercontent.com/e9ffcd896989f70ce25776275d4dac2706128f88aa5df3c0527d20509490441b/68747470733a2f2f696d672e736869656c64732e696f2f62756e646c6570686f6269612f6d696e2f6265747465722d646e692e7376673f7374796c653d666c61742d737175617265)

[![Better DNI](img/bc71830f0f1bd2cc909dcb02faa417a8.png)T2】](https://raw.githubusercontent.com/singuerinc/better-dni/master/logo.png)

最快的西班牙人 DNI(聂/ NIF)验证出来了。

## 装置

```
// npm
npm i better-dni
// yarn
yarn add better-dni
```

Enter fullscreen mode Exit fullscreen mode

## 使用

### is valid/isnif/isnie/ctrl lchar

```
import { isValid, isNIF, isNIE, ctrlChar } from "better-dni";
// validates any type of DNI (NIE or NIF)
isValid("Z7662566Y"); //=> true
// checks if it is a valid NIF
isNIF("06672804K"); //=> true

// checks if it is a valid NIE
isNIE("X1302311M"); //=> true

// returns the control letter in upper case
ctrlChar("X1302311M"); //=> 'M'
ctrlChar("X1302311"); //=> 'M'
```

Enter fullscreen mode Exit fullscreen mode

### 发电机

#### NIF

```
import { randomNIF, randomNIFWith } from "better-dni";
randomNIF(); //=> '31719111H'

// returns a valid random NIF given the control letter
randomNIFWith("C"); // => '95652190C'

//
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/singuerinc/better-dni)

很快我发现有很多模块在做同样的事情…

复制别人已经做的有点无聊，所以我把我的目标改为

> ”创建更快的模块来验证 DNI”

在这个过程中，我发现了很多提高代码速度的方法，虽然有时候看起来有点丑，其中一些方法让代码速度提高了一倍。

> 请注意，使用新版本的 Node 会获得更好的结果。我认为[https://jsperf.com](https://jsperf.com)在速度上相当不错，因为它只用一两行代码就能独立运行，但当我在有整个模块的“普通”计算机上尝试时，差别相当大。我会说，你应该在不同的环境中测试你的代码，看看哪里可以优化。

以下是我的发现:

### str.substr() vs str.slice()

```
'0123456789'.slice(-9); // => '123456789'
'0123456789'.substr(-9); // => '123456789' 
```

Enter fullscreen mode Exit fullscreen mode

胜者:**切片** / x21 快！/ [测试](https://jsperf.com/better-dni-slice-vs-substr/1)

### tolpower case()与 toUpperCase()的对比

我必须承认，我以前从未想过为什么一种方法会比另一种方法快。我正在研究 V8 引擎代码，看起来[https://github.com/v8/v8/blob/master/src/string-case.cc#L16](https://github.com/v8/v8/blob/master/src/string-case.cc#L16)就是这个问题的答案。

```
'A'.toLowerCase(); // => 'a'

'a'.toUpperCase(); // => 'A' 
```

Enter fullscreen mode Exit fullscreen mode

如果需要比较两个字符串，这尤其有用。

在比较它们之前，最好把它们转换成小写。

获胜者: **toLowerCase** /稍快/ [测试](https://jsperf.com/better-dni-lower-case-vs-upper-case/1)

<figure>[![](img/9661e065badfc9f57b0ce4bc9aa8f288.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cAX_vpeh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuQjccedT8iMRt8M6EgPfnw.jpeg) 

<figcaption>与速度相关的随机图像。所有岗位都需要一个。照片由[詹姆斯·特拉夫](https://unsplash.com/photos/ukTd6UiQbLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/fast?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

拍摄

### indexOf() vs 对象属性

```
const y = 'xyz'.indexOf('y'); // => 1

const y = {x: 0, y: 1, z: 2}['y'] // => 1 
```

Enter fullscreen mode Exit fullscreen mode

获胜者:的**指数/稍快/ [测试](https://jsperf.com/better-dni-index-of-vs-obj-prop/1)**

### 字符串串联 vs 模板文字

我不知道为什么我认为模板文字比简单的字符串连接更复杂。

```
const x = 'x';

'yyy' + x;

'yyy${x}'; // faster 
```

Enter fullscreen mode Exit fullscreen mode

获胜者:**模板文字** /稍快/ [测试](https://jsperf.com/better-dni-str-concat-vs-str-template/1)

### 转换为数字

```
parseInt('1234', 10); // => 1234

+'1234'; // => 1234 
```

Enter fullscreen mode Exit fullscreen mode

赢家:**+签** / x6.12 快！/ [测试](https://jsperf.com/better-dni-convert-to-number/1)

### Math.floor() vs 按位移位

我从这篇[博文](http://blog.blakesimpson.co.uk/read/58-fastest-alternative-to-math-floor-in-javascript)中获得了一些灵感。

```
Math.floor(1.123456789); // => 1

1.123456789 << 0; // => 1 
```

Enter fullscreen mode Exit fullscreen mode

获胜者:**按位移位** /在 jsperf 中稍快，但 Math.floor()在我的 MacBook Pro 中表现更好。/ [测试](https://jsperf.com/better-dni-floor-vs-bitwise/1)

### 其他可以帮助你的代码运行得更快的技巧

#### 提示 1

先做简单的验证，然后尽快返回。看看这些片段:

```
if(someComplexValidation(value) && value.length !== 9) return; 
```

Enter fullscreen mode Exit fullscreen mode

vs

```
if(value.length !== 9 && someComplexValidation(value)) return; 
```

Enter fullscreen mode Exit fullscreen mode

是同一个代码，对吗？首先进行“简单的”验证，这样如果第一次验证没有通过，您的代码就不会运行和返回。

#### 提示 2

避免使用像“toUpperCase()”这样昂贵的方法，尝试使用聪明的替代方法:

```
const n = 'XYZ'.indexOf('y'.toUpperCase()); // => 2 
```

Enter fullscreen mode Exit fullscreen mode

vs

```
const n = 'XYZxyz'.indexOf('y') % 3; // => 2 
```

Enter fullscreen mode Exit fullscreen mode

#### 提示 3

如果你知道你可以用不同的方法达到相同的结果，找到一个更适合每个具体情况的方法:

```
const x1 = 'xyz'.substr(0, 1); // => 'x'

const x2 = 'xyz'[0]; // => 'x'

const x3 = 'xyz'.slice(0, 1); // => 'x' 
```

Enter fullscreen mode Exit fullscreen mode

我希望这能帮助你优化你的代码！

你想有所贡献，让它更快吗？打开一个拉式请求:

[新格力公司/better-dni](https://github.com/singuerinc/better-dni/)