# 一元解析器组合子:交互式 JS 教程(Pt。1)

> 原文：<https://dev.to/glebec/monadic-parser-combinators-an-interactive-js-tutorial-pt-1-3jbo>

*解析*将串行数据解析成结构化的结果，是静态代码分析和编译的关键步骤。解析器还演示了各种功能概念，包括纯度、组成和单子。

在这个交互式教程中，我们将实现一个简单的解析器组合子库。组合子允许程序员通过使用少量的合作效用函数来轻松构建复杂的程序。

```
const color  = P.either(P.literal('red'), P.literal('blue'))
const animal = P.either(P.literal('zebra'), P.literal('giraffe'))
const spaces = P.many1(P.literal('  '))
const tallTale =
    color .chain(c =>
    spaces.chain(_ =>
    animal.chain(a => P.of(`There was a ${c}  ${a}.`)))) 
```

Enter fullscreen mode Exit fullscreen mode

目的一部分是为了学习解析，一部分是为了学习函数式编程。不需要任何经验。

## 动机

串行数据，例如来自文件或网络有效载荷的数据，通常必须在程序能够处理它之前被分析成结果。例如:

*   [HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Getting_started#What_is_HTML) 是页面内容的字符串格式。浏览器将 HTML 解析成 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) ，这是一个内存节点树，可以通过查询和操作来影响呈现的网页。
*   JSON 是嵌套数据的字符串格式，通常用于配置文件或网络负载。程序可以使用 [`JSON.parse`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 将 JSON 字符串转换成 JavaScript 对象，在运行时可以轻松读取和更新。
*   ESLint 是一个静态代码分析工具，用于检测错误和风格偏差。ESLint 使用一个 JavaScript 解析器( [Espree](https://github.com/eslint/espree) )来读取作者的程序(一个文本文件)并识别潜在的问题。

此外，一类名为*编译器*的程序更进一步，将解析树向下折叠成转换后的字符串格式。因此，编译器充当一种正式语言中的字符串到另一种正式语言中的字符串的翻译器。

```
const codeES2018 = 'num => num + 1'
const codeES5 = compileToES5(codeES2018)
console.log(codeES5) // 'function (num) { return num + 1 }' 
```

Enter fullscreen mode Exit fullscreen mode

*   [V8](https://developers.google.com/v8/) 是一个 JavaScript 引擎，使用编译器将 JavaScript 翻译成可执行的机器码。
*   [Babel](https://babeljs.io/) 将以现代和/或特定领域语法(如 ES2018 + JSX)编写的 JavaScript 编译成较旧的语法(如 ES5)。
*   [更漂亮](https://prettier.io/)将非标准格式的 JavaScript 编译成格式一致的 JavaScript。

在典型的编译器中，解析被称为*前端*，代码生成被称为*后端*。

```
 infix       FE (parser)       +      BE (generator)      postfix
 "2 + 3 * 5"         ->          / \           ->          "2 3 5 * +"
                                2   *
                               / \
                              3   5 
```

Enter fullscreen mode Exit fullscreen mode

然而，也有可能在解析步骤期间生成结果*。在这种情况下，不会构建显式的树，尽管解析器在递归输入字符串时会隐式地遵循树结构。*

# 解析器

通常，解析器从一些串行输入的开头提取结构。输入可以是一个普通的字符串，尽管一些解析器可能期望一个 [*流*](https://en.wikipedia.org/wiki/Stream_(computing)) (顺序消耗源，例如生成器或可观察对象) [*令牌*](https://en.wikipedia.org/wiki/Lexical_analysis#Token) (记录语言类型和词汇内容的对象)。

让我们从简单的开始，把解析器看作是从字符串到结果的函数。

```
// parse :: String -> *
const parse = s => ? 
```

Enter fullscreen mode Exit fullscreen mode

这回避了我们的解析器产生什么的问题。答案要看问题。许多解析器产生树节点，但是可以编写解析器来构建任何想要的结果，包括原始字符串、数字、函数等等。

## 处理失败

现在让我们只捕获我们的解析器匹配的文字字符串值(或*)。下面是一个(不完整的)解析器，用来匹配并产生词位“三角龙”:* 

```
// parseTri :: String -> String
const parseTri = s => "Triceratops" 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，这个解析器坏了。如果我们的字符串包含错误的恐龙呢？解析“霸王龙”不应该得出“三角龙”。我们需要一种方法来**发出故障信号**。

你会如何解决这个问题？想出自己的方法；您的解析函数应该能够接受以下字符串，要么得到“Triceratops ”,要么指出(以某种方式)解析失败。

`// parseTri :: String -> String // change me const parseTri = s => 'Triceratops' // change me // change parseTri so that it can indicate failure console.log(parseTri('Triceratops')) // should succeed console.log(parseTri('Triceratops rules')) // should succeed console.log(parseTri('I <3 Triceratops')) // should fail console.log(parseTri('Pteranodons')) // should fail console.log(parseTri('T. Rex')) // should fail console.log(parseTri('')) // should fail`

### 信令故障:也许？

```
type Parser = String -> Maybe String 
```

Enter fullscreen mode Exit fullscreen mode

一个有经验的函数式程序员可能会使用[和类型](https://medium.com/fullstack-academy/better-js-cases-with-sum-types-92876e48fd9f)，比如 [`Maybe`单子](https://jrsinclair.com/articles/2016/marvellously-mysterious-javascript-maybe-monad/)。然而，现在正确地处理这个概念会使这篇文章偏离主题，所以我们稍后会回到`Maybe`。

### 信令失败:数组？

```
type Parser = String -> [String] 
```

Enter fullscreen mode Exit fullscreen mode

函数解析器通常将结果表示为列表。因此，失败可以由空列表`[]`来表示，并且如果必要的话，可以捕获多个成功(来自模糊的语法)。不明确的语法超出了本文的范围，所以我们不需要严格的列表。

### 信令失败:空

```
type Parser = String -> String | Null 
```

Enter fullscreen mode Exit fullscreen mode

`Maybe`和列表都是处理失败的好方法，但是现在我们将坚持使用 JS 开发人员熟悉的方法:`null`。这种`null`价值，其发明者[东尼·霍尔](https://en.wikipedia.org/wiki/Tony_Hoare)曾称他的[“十亿美元的错误”](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)，有着严重的缺陷。然而，它是习惯性的，平易近人的，而且目前已经足够了。

这是我们新的解析器。在一些弦上试试，看看会发生什么:

`// parseTri :: String -> String | Null const parseTri = s => s.startsWith('Triceratops') ? 'Triceratops' : null console.log(parseTri('...try strings here...'))`

## 处理剩菜

当我们从一个字符串中解析一个结果时，在大多数情况下我们只会消耗部分输入。其余的将有我们可以解析的其他值，这意味着我们需要跟踪从哪里恢复。在像 JavaScript 这样的可变设置中，人们可能想创建一个共享的`index`值。

```
let index = 0
const parseTri = s => {
    const remainder = s.slice(index)
    if (remainder.startsWith('Triceratops')) {
        index += 'Triceratops'.length
        return 'Triceratops'
    }
    return null
} 
```

Enter fullscreen mode Exit fullscreen mode

这对于解析单个字符串很有效:

`let index = 0 const parseTri = s => { const remainder = s.slice(index) if (remainder.startsWith('Triceratops')) { index += 'Triceratops'.length return 'Triceratops' } return null }` `// (index & parseTri are in scope but hidden) const string = 'TriceratopsTriceratops' const res1 = parseTri(string) // 'Triceratops' const res2 = parseTri(string) // 'Triceratops' const res3 = parseTri(string) // null console.log(res1, res2, res3)`

然而，共享的可变状态会很快导致问题。如果我们想随后解析一个不同的字符串呢？

`let index = 0 const parseTri = s => { const remainder = s.slice(index) if (remainder.startsWith('Triceratops')) { index += 'Triceratops'.length return 'Triceratops' } return null } const string = 'TriceratopsTriceratops' const res1 = parseTri(string) // 'Triceratops' const res2 = parseTri(string) // 'Triceratops' const res3 = parseTri(string) // null` `// (index, parseTri, and res1–res3 are in scope but hidden) const string2 = 'Triceratops rules' const res4 = parseTri(string2) console.log(res4) // null // oops!`

当然，解决办法是有的。我们可以创建一个`Parser`类，它的实例管理内部状态，或者创建一个更高阶的`makeParser`函数来关闭状态。这两种解决方案都埋没了国家，却没有真正消灭它；调试这样的隐藏状态有时甚至更具挑战性。

### 纯代币消费

原来对于这个问题，我们其实并不需要可变状态。在函数式编程中，我们更喜欢*纯*的解决方案。

纯函数是从输入到输出的无状态确定性映射，没有副作用。换句话说，纯函数有输出，但既不依赖也不改变外部宇宙。如果你能把你的函数定义为一个(可能是无限的)输入输出表，它就是纯粹的。试归纳以下`f1`、`f2`、`f3`是如何定义的:

| f1 输入 | f1 输出 | f2 英寸 | f2 输出 | f3 英寸 | f3 输出 |
| --- | --- | --- | --- | --- | --- |
| `'hi'` | `2` | `[a, b]` | `b` | `0` | `-2` |
| `'what'` | `4` | `[0, 9, 1]` | `9` | `1` | `-1` |
| `'pow'` | `3` | `[0]` | `undefined` | `2` | `2` |
| `'forest'` | `6` | `[]` | `undefined` | `3` | `7` |
| `'a'` | `1` | `[1, 2, 3]` | `2` | `4` | `14` |
| `''` | `0` | `[z, y]` | `y` | `5` | `23` |
| … | … | … | … | … | … |

如果一个解析器需要指出下一个解析应该从哪里开始，这意味着剩余的输入本身应该是一个返回值。因此，我们的解析器将返回两个东西:一个解析结果和剩余的输入。

```
type Parser = String -> String & String 
```

Enter fullscreen mode Exit fullscreen mode

你怎么能写一个返回两个东西的函数呢？可行的方法不止一种；在下面提出你自己的方法，然后继续阅读我们的观点。

`// parseTri :: String -> String & String const parseTri = s => s.startsWith('Triceratops') ? 'Triceratops' // fix me to also return leftover input : null // should succeed with "" as a remainder console.log(parseTri('Triceratops')) // should succeed with " rules" as a remainder console.log(parseTri('Triceratops rules')) // should fail console.log(parseTri('T. Rex'))`

### 元组和朋友

因为函数式编程涉及大量流入和流出函数的数据，所以函数式语言通常有一个轻量级的数据结构，用于将值打包在一起:元组。

```
-- Haskell
myTuple = ("hello", 99) -- a 2-tuple
str = fst myTuple -- "hello"
num = snd myTuple -- 99 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 没有元组，但是有对象和数组(实际上是对象的一种)。我们可以很容易地模拟元组:

```
const myTuple = ['hello', 99]
const str = myTuple[0] // 'hello'
const num = myTuple[1] // false 
```

Enter fullscreen mode Exit fullscreen mode

```
const myTuple = { str: 'hello', num: 99 }
const { str } = myTuple // 'hello'
const { num } = myTuple // false 
```

Enter fullscreen mode Exit fullscreen mode

数组更简洁，但是对象更有表现力。你可能在上面的解决方案中使用了其中的一个；我们将使用对象。

`// parseTri :: String -> { result: String, remainder: String } | Null const parseTri = s => s.startsWith('Triceratops') ? { result: 'Triceratops', remainder: s.slice(11) } : null console.log(parseTri('Triceratops')) console.log(parseTri('Triceratops is cool')) console.log(parseTri('T. Rex'))`

#### 旁注:同构

*n*-元组和*n*-元素 JS 数组(当单独用于存储数据时)实际上是 [*同构的*](https://kseo.github.io/posts/2016-12-25-type-isomorphism.html) 。如果可以定义一对函数`a2b`和`b2a`，则设置`A`和`B`是同构的，使得:

*   `a2b`将`A`中的所有值映射到`B`中的值
*   `b2a`将`B`中的所有值映射到`A`中的值
*   `b2a(a2b(a)) = a`
*   `a2b(b2a(b)) = b`

同构是一个[丰富的主题](https://stackoverflow.com/questions/11245183/importance-of-isomorphic-functions),有一些令人愉快的结果，但是结论是，对于一个给定的用例，知道两种数据类型什么时候是等价的是很好的。

* * *

*思想难题:为什么二值对象**而不是**可能同构于 2-el 数组？翻译中会丢失哪些信息？*

* * *

### 线程解析器

我们的解析器现在可以报告输入的哪一部分没有被使用，但是我们实际上如何使用这些信息呢？一次解析剩下的字符串成为下一次解析的输入。完成下面的代码。

`const assert = require('assert') const parseTri = s => s.startsWith('Triceratops') ? { result: 'Triceratops', remainder: s.slice(11) } : null const solution = [ "const firstParse = parseTri(string)", "const secondParse = parseTri(firstParse.remainder)", "const thirdParse = parseTri(secondParse.remainder)" ].join('\n')` `// parseTri :: String -> { result: String, remainder: String } | Null // parseTri is in scope in this snippet. You can refer back // to its definition, but you won't need to edit it. const string = 'TriceratopsTriceratops' // use parseTri to obtain the three values below: const firstParse = undefined const secondParse = undefined const thirdParse = undefined // console.log(solution) assert.deepStrictEqual(firstParse, { result: 'Triceratops', remainder: 'Triceratops' }, 'first parse works') assert.deepStrictEqual(secondParse, { result: 'Triceratops', remainder: '' }, 'second parse works') assert.equal(thirdParse, null, 'third parse works')`

要查看我们的解决方案，您可以记录隐藏变量`solution`。

## 概化

我们有一个解析器可以解析“三角龙”——没什么意思。给其他恐龙写几个解析器。

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const testParsers = () => registerTests(t => { t.deepEqual( parseRex('T. Rex'), { result: 'T. Rex', remainder: '' }, 'parseRex parses "T. Rex"' ) t.deepEqual( parseRex('T. Rex is scary'), { result: 'T. Rex', remainder: ' is scary' }, 'parseRex parses "T. Rex is scary"' ) t.equal( parseRex('Triceratops'), null, 'parseRex fails to parse "Triceratops"' ) t.deepEqual( parseRaptor('Velociraptor'), { result: 'Velociraptor', remainder: '' }, 'parseRaptor parses "Velociraptor"' ) t.deepEqual( parseRaptor('Velociraptor is scary'), { result: 'Velociraptor', remainder: ' is scary' }, 'parseRaptor parses "Velociraptor is scary"' ) t.equal( parseRaptor('Triceratops'), null, 'parseRaptor fails to parse "Triceratops"' ) t.end() }) const solution = [ "const parseRex = s => s.startsWith('T. Rex')", " ? { result: 'T. Rex', remainder: s.slice(6) }", " : null", "", "const parseRaptor = s => s.startsWith('Velociraptor')", " ? { result: 'Velociraptor', remainder: s.slice(12) }", " : null" ].join('\n')` `// parseRex :: String -> { result: String, remainder: String } | Null const parseRex = undefined // matches "T. Rex" // parseRaptor :: String -> { result: String, remainder: String } | Null const parseRaptor = undefined // matches "Velociraptor" // console.log(solution) await testParsers()`

这是相当重复的。我们来概括一下，做一个`parseLiteral`函数。

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const testParsers = () => registerTests(t => { t.deepEqual( parseRex('T. Rex'), { result: 'T. Rex', remainder: '' }, 'parseRex parses "T. Rex"' ) t.deepEqual( parseRex('T. Rex is scary'), { result: 'T. Rex', remainder: ' is scary' }, 'parseRex parses "T. Rex is scary"' ) t.equal( parseRex('Triceratops'), null, 'parseRex fails to parse "Triceratops"' ) t.deepEqual( parseRaptor('Velociraptor'), { result: 'Velociraptor', remainder: '' }, 'parseRaptor parses "Velociraptor"' ) t.deepEqual( parseRaptor('Velociraptor is scary'), { result: 'Velociraptor', remainder: ' is scary' }, 'parseRaptor parses "Velociraptor is scary"' ) t.equal( parseRaptor('Triceratops'), null, 'parseRaptor fails to parse "Triceratops"' ) t.end() }) const solution = [ "const parseLiteral = literal => s => s.startsWith(literal)", " ? { result: literal, remainder: s.slice(literal.length) }", " : null", "", "const parseRex = parseLiteral('T. Rex')", "", "const parseRaptor = parseLiteral('Velociraptor')" ].join('\n')` `// parseLiteral :: String -> // (String -> { result: String, remainder: String } | Null) const parseLiteral = literal => undefined // parseRex :: String -> { result: String, remainder: String } | Null const parseRex = undefined // use parseLiteral // parseRaptor :: String -> { result: String, remainder: String } | Null const parseRaptor = undefined // use parseLiteral // console.log(solution) await testParsers()`

解决上述问题展示了一种常见的功能技术。*高阶*函数自己取和/或返回函数；`parseLiteral`※后者。`parseLiteral`不是为每个字符串硬编码单独的解析器，而是一般化，从字符串映射到解析器:

| 线 | `parseLiteral(string)` |
| --- | --- |
| `'hi'` | `'hi'`的解析器 |
| `'yo'` | `'yo'`的解析器 |
| `'wow'` | `'wow'`的解析器 |
| … | … |

### 不同的结果类型

解析器*在字符串上匹配*，但是它们不需要在字符串中得到*结果*。为产生数字的数字串编写解析器。

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const testParsers = () => registerTests(t => { t.deepEqual( parseNum0('0'), { result: 0, remainder: '' }, 'parseNum0 parses "0"' ) t.deepEqual( parseNum0('0 is cool'), { result: 0, remainder: ' is cool' }, 'parseNum0 parses "0 is cool"' ) t.equal( parseNum0('1'), null, 'parseNum0 fails to parse "1"' ) t.deepEqual( parseNum1('1'), { result: 1, remainder: '' }, 'parseNum1 parses "1"' ) t.deepEqual( parseNum1('1 is cool'), { result: 1, remainder: ' is cool' }, 'parseNum1 parses "1 is cool"' ) t.equal( parseNum1('0'), null, 'parseNum1 fails to parse "0"' ) t.end() }) const solution = [ "const parseNum0 = s => s.startsWith('0')", " ? { result: 0, remainder: s.slice(1) }", " : null", "", "const parseNum1 = s => s.startsWith('1')", " ? { result: 1, remainder: s.slice(1) }", " : null" ].join('\n')` `// parseNum0 :: String -> { result: Number, remainder: String } | Null const parseNum0 = undefined // result should be 0, not '0' // parseNum1 :: String -> { result: Number, remainder: String } | Null const parseNum1 = undefined // result should be 1, not '1' // console.log(solution) await testParsers()`

一旦我们开始将我们的解析器用于实际目的，将原始的词位转换成特定于我们用例的处理结果将会很方便。

### 类型，签名，&别名

我们现在已经看到了两种不同的解析器:一种产生字符串，另一种产生数字。

```
parseRex  :: String -> { result: String, remainder: String } | Null
parseNum1 :: String -> { result: Number, remainder: String } | Null 
```

Enter fullscreen mode Exit fullscreen mode

我们还没有特别注意这些评论，更喜欢通过例子来教授，但这些是*型签名*的例子。类型签名记录了一个值属于什么**集合**。例如，`Bool`类型是两个值的集合:`true`和`false`。你可以把符号`::`读作“has type”或者“is in the set”:

```
true  :: Bool -- `true`  is in the set `Bool`
false :: Bool -- `false` is in the set `Bool` 
```

Enter fullscreen mode Exit fullscreen mode

函数也属于类型。`!`(或“非”)函数恰好在将`Bool`中的值映射到`Bool`T6 中的值的*函数集中。我们将此标记为`(!) :: Bool -> Bool`。下面是定义的`!`函数:*

| Bool input | Bool output |
| --- | --- |
| `true` | `false` |
| `false` | `true` |

* * *

*思想谜题:`Bool -> Bool`类型的其他函数正好有**三个**。你能定义它们吗？记住，每个函数都是两行的表格。*

* * *

函数是从一种**类型**到另一种**类型**的映射(例如`Int -> Bool`、`String -> String`或`Object -> String`)。相应地，函数式语言经常强调它们的类型系统。ML 和类似 OCaml、F#、ReasonML、Miranda、Haskell、PureScript、Idris 和 Elm 的相关语言是具有复杂类型的语言的例子，包括 [*代数数据类型*](https://codewords.recurse.com/issues/three/algebra-and-calculus-of-algebraic-data-types) 和完整的 [*类型推断*](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system) 。

JavaScript 有一个(有时是臭名昭著的)动态类型系统，并且缺少官方的类型符号。这里的语法借用了 Haskell，类似于 JS 实用程序库 [Ramda](http://ramdajs.com/) 使用的系统。这个问题被[拉姆达维基](https://github.com/ramda/ramda/wiki/Type-Signatures)和[幻境文档](https://github.com/fantasyland/fantasy-land#type-signature-notation)很好地解释了。

我们现在提出这个问题，是因为写出我们的整个类型的解析器开始变得很麻烦。`parseRex`和`parseNum1`型签名仅在一个位置不同，`result` :

```
parseRex  :: String -> { result: String, remainder: String } | Null
parseNum1 :: String -> { result: Number, remainder: String } | Null 
```

Enter fullscreen mode Exit fullscreen mode

因此，从现在开始我们将使用`Parser a`来表示“一个解析函数，它的`result`是类型`a`:

```
type Parser a = String -> { result: a, remainder: String } | Null 
```

Enter fullscreen mode Exit fullscreen mode

这个*类型别名*只是我们将用于文档目的的简写。这将极大地清理我们的注释，并使讨论解析器变得更加容易。比如现在我们可以更简洁的把`parseRex`和`parseNum1`分类:

```
parseRex  :: Parser String
parseNum1 :: Parser Number 
```

Enter fullscreen mode Exit fullscreen mode

你自己试试。使用速记简化我们的`parseLiteral`签名:

`const solution = [ "parseLiteral :: String -> Parser String" ].join('\n')` `// long version: // parseLiteral :: String -> // (String -> { result: String, remainder: String } | Null) // short version (fill in): // parseLiteral :: ??? console.log(solution)`

## 最后的组合子

如果我们想匹配不止一种可能性呢？实现以下解析器。现有解析器在范围内，在您的定义中使用它们:

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const parseLiteral = literal => s => s.startsWith(literal) ? { result: literal, remainder: s.slice(literal.length) } : null const parseTri = parseLiteral('Triceratops') const parseRex = parseLiteral('T. Rex') const parseRaptor = parseLiteral('Velociraptor') const testParsers = () => registerTests(t => { t.deepEqual( parseCarnivore('T. Rex is scary'), { result: 'T. Rex', remainder: ' is scary' }, 'parseCarnivore parses "T. Rex is scary"' ) t.deepEqual( parseCarnivore('Velociraptor is fast'), { result: 'Velociraptor', remainder: ' is fast' }, 'parseCarnivore parses "Velociraptor is fast"' ) t.equal( parseCarnivore('Triceratops eats grass'), null, 'parseCarnivore fails to parse "Triceratops eats grass"' ) t.deepEqual( parseBigDino('T. Rex has small arms'), { result: 'T. Rex', remainder: ' has small arms' }, 'parseBigDino parses "T. Rex has small arms"' ) t.deepEqual( parseBigDino('Triceratops has three horns'), { result: 'Triceratops', remainder: ' has three horns' }, 'parseBigDino parses "Triceratops has three horns"' ) t.equal( parseBigDino('Velociraptor hunts in packs'), null, 'parseBigDino fails to parse "Velociraptor hunts in packs"' ) t.end() }) const solution = [ "// parseCarnivore, parseBigDino :: Parser String", "const parseCarnivore = s => parseRex(s) || parseRaptor(s)", "const parseBigDino = s => parseRex(s) || parseTri(s)" ].join('\n')` `// parseTri :: Parser String // parseRex :: Parser String // parseRaptor :: Parser String // parseCarnivore :: Parser String const parseCarnivore = undefined // match "T. Rex" OR "Velociraptor" // parseBigDino :: Parser String const parseBigDino = undefined // match "T. Rex" OR "Triceratops" // console.log(solution) await testParsers()`

再次，这是越来越重复。面对重复，函数式程序员会怎么做？一概而论！编写一个函数`either`,它接受两个解析器并返回一个匹配其中一个的新解析器。

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const parseLiteral = literal => s => s.startsWith(literal) ? { result: literal, remainder: s.slice(literal.length) } : null const parseTri = parseLiteral('Triceratops') const parseRex = parseLiteral('T. Rex') const parseRaptor = parseLiteral('Velociraptor') const testParsers = () => registerTests(t => { t.deepEqual( parseCarnivore('T. Rex is scary'), { result: 'T. Rex', remainder: ' is scary' }, 'parseCarnivore parses "T. Rex is scary"' ) t.deepEqual( parseCarnivore('Velociraptor is fast'), { result: 'Velociraptor', remainder: ' is fast' }, 'parseCarnivore parses "Velociraptor is fast"' ) t.equal( parseCarnivore('Triceratops eats grass'), null, 'parseCarnivore fails to parse "Triceratops eats grass"' ) t.deepEqual( parseBigDino('T. Rex has small arms'), { result: 'T. Rex', remainder: ' has small arms' }, 'parseBigDino parses "T. Rex has small arms"' ) t.deepEqual( parseBigDino('Triceratops has three horns'), { result: 'Triceratops', remainder: ' has three horns' }, 'parseBigDino parses "Triceratops has three horns"' ) t.equal( parseBigDino('Velociraptor hunts in packs'), null, 'parseBigDino fails to parse "Velociraptor hunts in packs"' ) t.end() }) const solution = [ "// either :: (Parser a, Parser b) -> Parser (a | b)", "const either = (p1, p2) => s => p1(s) || p2(s)" ].join('\n')` `// parseLiteral :: String -> Parser String // parseTri :: Parser String // parseRex :: Parser String // parseRaptor :: Parser String // either :: (Parser a, Parser b) -> Parser (a | b) const either = (p1, p2) => undefined // parseCarnivore, parseBigDino :: Parser String const parseCarnivore = either(parseRex, parseRaptor) const parseBigDino = either(parseRex, parseTri) // console.log(solution) await testParsers()`

恭喜你，你已经写了一个*组合子*。

### Combi-who？

*Combinator* 像许多编程术语一样，被重载了——它在不同的上下文中有不同的含义。

*   [组合逻辑](https://en.wikipedia.org/wiki/Combinatory_logic)是研究作用于函数的函数。例如，`I`组合子(`x => x`)接受一个函数并返回相同的函数。在这个领域，“组合子”正式的意思是没有*自由*(未绑定)*变量*的函数。
*   组合子模式是一种构建灵活库的策略，在这种模式中，少量的合作效用函数构建了丰富的结果(这些结果反过来可以成为这些相同函数的新输入)。
    *   例如，CSS“组合子”接受 CSS 选择器并产生新的、更复杂的选择器。`div`和`h1`都是图元选择器；使用*直接子* ( `>`)组合子，您可以创建新的`div > h1`选择器。

我们将把*解析器组合子*定义为一个函数，它接受一个或多个解析器，并返回一个新的解析器。一些例子:

```
either :: (Parser a, Parser b) -> Parser (a | b)
both   :: (Parser a, Parser b) -> Parser [a, b]
any    :: (...Parser *)        -> Parser *
many   ::  Parser a            -> Parser [a] 
```

Enter fullscreen mode Exit fullscreen mode

您已经定义了`either`；接下来在`both`和`any`尝试一下。对于手动测试，`parseLiteral`和我们所有的 dino 解析器都在范围之内:

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const parseLiteral = literal => s => s.startsWith(literal) ? { result: literal, remainder: s.slice(literal.length) } : null const parseTri = parseLiteral('Triceratops') const parseRex = parseLiteral('T. Rex') const parseRaptor = parseLiteral('Velociraptor') const testTriRexViaBoth = () => registerTests(t => { const parseTriRex2 = both(parseTri, parseRex) t.deepEqual( parseTriRex2('TriceratopsT. Rex'), { result: ['Triceratops', 'T. Rex'], remainder: '' }, 'parseTriRex parses "TriceratopsT. Rex"' ) t.deepEqual( parseTriRex2('TriceratopsT. Rex!!!'), { result: ['Triceratops', 'T. Rex'], remainder: '!!!' }, 'parseTriRex parses "TriceratopsT. Rex!!!"' ) t.equal( parseTriRex2('TriceratopsT. Bone'), null, 'parseTriRex fails to parse "TriceratopsT. Bone"' ) t.equal( parseTriRex2('Triangles'), null, 'parseTriRex fails to parse "Triangles"' ) t.end() }) const solution = [ "const both = (p1, p2) => s => {", " const r1 = p1(s)", " if (!r1) return null", " const r2 = p2(r1.remainder)", " if (!r2) return null", " return {", " result: [r1.result, r2.result],", " remainder: r2.remainder", " }", "}" ].join('\n')` `// both takes in a two parsers, and makes a parser that matches // the first one and then the second (or fails if EITHER of them // fails). It packages the two successes into an array. // both :: (Parser a, Parser b) -> Parser [a, b] const both = undefined // parseTriRex :: Parser String const parseTriRex = both(parseTri, parseRex) console.log(parseTriRex('TriceratopsT. Rex!')) // succeeds console.log(parseTriRex('Triceratops & T. Rex')) // fails // console.log(solution) await testTriRexViaBoth()`

另外，如果您需要，`either`也在范围内:

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const parseLiteral = literal => s => s.startsWith(literal) ? { result: literal, remainder: s.slice(literal.length) } : null const either = (p1, p2) => s => p1(s) || p2(s) const parseTri = parseLiteral('Triceratops') const parseRex = parseLiteral('T. Rex') const parseRaptor = parseLiteral('Velociraptor') const testDinoViaAny = () => registerTests(t => { const parseDino2 = any(parseTri, parseRex, parseRaptor) t.deepEqual( parseDino2('Velociraptor on the loose'), { result: 'Velociraptor', remainder: ' on the loose' }, 'parseDino parses "Velociraptor on the loose"' ) t.deepEqual( parseDino2('Triceratops for the win'), { result: 'Triceratops', remainder: ' for the win' }, 'parseDino parses "Triceratops for the win"' ) t.deepEqual( parseDino2('T. Rex is a king'), { result: 'T. Rex', remainder: ' is a king' }, 'parseDino parses "T. Rex is a king"' ) t.equal( parseDino2('Wooly mammoths migrate west'), null, 'parseDino fails to parse "Wooly mammoths migrate west"' ) t.end() }) const solution = [ "// any :: (...Parser *) -> Parser *", "const any = (...parsers) => parsers.reduce(either)", ].join('\n')` `// either :: (Parser a, Parser b) -> Parser (a | b) // any takes in a variable number of parsers, and makes a parser // that matches the first one to match (or fails if they ALL fail). // any :: (...Parser *) -> Parser * const any = undefined // parseDino :: Parser String const parseDino = any(parseTri, parseRex, parseRaptor) // console.log(solution) await testDinoViaAny()`

这是一个强大的概念。我们可以定义一些抽象的组合子，而不是为每一个可以想到的目的手工编写特定的解析器函数。输入简单的解析器，输出更强大的解析器；这些新的强大的解析器可以依次成为相同组合子的输入。因此，我们可以通过一些易于使用的工具构建丰富的行为。这就是**构图**的精髓——通过组合来构建。

`const parseLiteral = literal => s => s.startsWith(literal) ? { result: literal, remainder: s.slice(literal.length) } : null const parseTri = parseLiteral('Triceratops') const parseRex = parseLiteral('T. Rex') const parseRaptor = parseLiteral('Velociraptor') const either = (p1, p2) => s => p1(s) || p2(s) const both = (p1, p2) => s => { const r1 = p1(s) if (!r1) return null const r2 = p2(r1.remainder) if (!r2) return null return { result: [r1.result, r2.result], remainder: r2.remainder } } const any = (...parsers) => parsers.reduce(either)` `const parseDino = any(parseTri, parseRex, parseRaptor) const parseTwoDinos = both(parseDino, parseDino) console.log(parseTwoDinos('VelociraptorTriceratopsEtc.')) // succeeds console.log(parseTwoDinos('T. RexVelociraptor…')) // succeeds console.log(parseTwoDinos('TriceratopsNope')) // fails`

## 第一部分结论

下面是我们的解析器组合子库的当前状态。

```
// type Parser a = String -> { result: a, remainder: String } | Null

// parseLiteral :: String -> Parser String
const parseLiteral = literal => s => s.startsWith(literal)
    ? { result: literal, remainder: s.slice(literal.length) }
    : null

// either :: (Parser a, Parser b) -> Parser (a | b)
const either = (p1, p2) => s => p1(s) || p2(s)

// both :: (Parser a, Parser b) -> Parser [a, b]
const both = (p1, p2) => s => {
    const r1 = p1(s)
    if (!r1) return null
    const r2 = p2(r1.remainder)
    if (!r2) return null
    return {
        result: [r1.result, r2.result],
        remainder: r2.remainder
    }
}

// any :: (...Parser *) -> Parser *
const any = (...parsers) => parsers.reduce(either) 
```

Enter fullscreen mode Exit fullscreen mode

在第一部分中，我们介绍了:

*   什么是解析
*   解析器的一些用例
*   处理失败的一种(非功能性)方法
*   消除状态的一种(功能)方法
*   功能纯度
*   使用元组(或数组/对象)作为返回值
*   顺序线程解析器结果
*   类型、签名和别名
*   基本解析器组合子

在本文的后续文章中，我们将讨论一些更具挑战性的问题:

*   处理较长的序列
*   处理选择
*   函子和单子
*   模拟中缀函数符号
*   形式语法和语法分析理论
*   递归下降
*   怠惰

第 2 部分将在发布后链接到这里。

* * *

### 关于作者

Gabriel Lebec 是 Fullstack Academy of Code 的讲师，这是一个密集的 web 应用程序开发项目。他有数学和工作室艺术的学士学位，还有太多的爱好，包括对日本古董刀剑的艺术史研究。你可以在 [Medium](https://medium.com/@glebec) 、 [YouTube](https://www.youtube.com/channel/UCVqIaPVG27Nh64ZGWdFzSWQ/playlists) 、 [GitHub](https://github.com/glebec) 和 [Twitter](https://twitter.com/g_lebec) 看到更多 Gabriel 的内容。*