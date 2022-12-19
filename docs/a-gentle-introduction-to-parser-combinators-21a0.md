# 解析器组合子的简明介绍

> 原文：<https://dev.to/yelouafi/a-gentle-introduction-to-parser-combinators-21a0>

在本教程中，我们将构建一组解析器组合子。

## 什么是解析器组合子？

我们将分两步回答上述问题

1.  什么是解析器？
2.  和..什么是解析器组合子？

所以第一个问题:什么是解析器？

答:(最简单的形式)解析器是一个

1.  一项功能
2.  这需要一些原始序列形式的输入(比如字符串)
3.  并返回从原始输入构建的一些有意义的数据
4.  **或**如果原始输入与预期不符，则出现一些错误

这里有一个非常简单的例子。接受字符串的解析器。如果字符串代表一个有效的整数，则返回该整数，否则返回一个解析错误。

```
function parseInteger(input) {
  const match = /^\d+$/.exec(input);
  if (match != null) {
    return +match[0];
  }
  return new Error("Invalid integer");
} 
```

```
$ parseInteger("12")
  >> 12

$ parseInteger("hey")
  >> Error: Invalid integer 
```

不错，但是
呢

```
$ parseInteger("12hey")
  >> Error: Invalid integer 
```

因为我们使用了`^` & `$`我们的正则表达式检查整个输入是否是一个有效的整数。如果这是我们唯一想要解析的东西，这是有意义的。然而，我们经常想要解析更复杂的东西。

## 测序解析器

这里是另一个例子，我们要解析下面的序列

1.  整数
2.  一个“+”字符
3.  然后是另一个整数

并返回在(1)和(3)中获得的两个数之和

我们将保持简单，不允许 3 个步骤之间有空格。那么我们如何接近它呢？

我们已经有了我们的`parseInteger`函数。我们可以用另一个函数`parsePlus`重用它。但是我们需要重新思考我们之前的定义。

让我们考虑一下:要解析上面的序列，我们需要依次运行 3 个解析器(即函数)。但这并不像构造简单的函数那么简单。从一个步骤传递到另一个步骤需要一些粘合代码。

1.  第一个`parseInteger`将尝试从输入开始解析一个整数
2.  如果(1)返回一个错误，那么我们停止解析并返回那个错误
3.  否则，我们用字符串的其余部分调用第二个解析器

但是为了实现(3 ),我们必须从第一个解析器获得字符串的其余部分。所以现在我们的解析器函数应该返回

1.  如果解析器失败，则为错误
2.  或者在成功的情况下，结果加上剩余的输入

因此，使用(2)中的返回值，我们可以调用序列中的下一个解析器来解析输入的其余部分。

在重写之前，让我们先对解析器接口做一些修改。

```
// We'll use our own error description
function failure(expected, actual) {
  return { isFailure: true, expected, actual };
}

function success(data, rest) {
  return { data, rest };
}

// And for our main parsing, we'll invoke this function
function parse(parser, input) {
  const result = parser(input);
  if (result.isFailure) {
    throw new Error(`Parse error.
        expected ${result.expected}.
        instead found '${result.actual}'
    `);
  } else {
    return result;
  }
} 
```

现在让我们修改 parseInteger 函数以适应新的接口(从现在开始我们将使用更简洁的命名约定:例如`ìnteger`in stad of`parseInteger`。这将使我们的代码更具可读性，因为我们将定义更复杂的解析器)

```
function integer(input) {
  // note we removed $ from the end of the regular expression
  const match = /^\d+/.exec(input);
  if (match != null) {
    const matchedText = match[0];
    return success(+matchedText, input.slice(matchedText.length));
  }
  return failure("an integer", input);
} 
```

```
$ parse(integer, "12")
  >> {data: 12, rest: ""}

$ parse(integer, "hey")
  Uncaught Error: Parse error.
        expected an integer.
        instead found 'hey'

$ parse(integer, "12hey")
  >> {data: 12, rest: "hey"} 
```

好吧。让我们编写第二个解析器来解析'+'字符。这个简单多了

```
function plus(input) {
  if (input[0] === "+") {
    return success("+", input.slice(1));
  }
  return failure("'+'", input);
} 
```

和 2 个快速测试

```
$ parse(plus, '+33')
  >> {data: "+", rest: "33"}

$ parse(plus, '33+')
  >> Uncaught Error: Parse error.
        expected '+'.
        instead found '33+' 
```

现在我们将编写我们的主解析器，它将解析整个序列

```
function plusExpr(input) {
  // step 1 : parse the first integer
  const result1 = integer(input);
  if (result1.isFailure) return result1;
  const { data: int1, rest: input1 } = result1;

  // step 2 : parse "+"
  const result2 = plus(input1);
  if (result2.isFailure) return result2;
  const { rest: input2 } = result2;

  // step 3 : parse the second integer
  const result3 = integer(input2);
  if (result3.isFailure) return result3;
  const { data: int2, rest: input3 } = result3;

  // one last check
  if (input3.length > 0) {
    return failure("end of input", input3);
  }
  // everything is allright. returns the final result
  return success(int1 + int2, input3);
} 
```

```
$ parse(plusExpr, "12+34")
  >> {data: 46, rest: ""}

$ parse(plusExpr, "12a+34")
  >> Uncaught Error: Parse error.
        expected '+'.
        instead found 'a+34'

parse(plusExpr, "12-34")
>> Uncaught Error: Parse error.
        expected '+'.
        instead found '-34'

$ parse(plusExpr, "12+34rest")
  >> Uncaught Error: Parse error.
        expected end of input.
        instead found '12+34rest' 
```

到目前为止一切顺利。但是为了让我们的解析器实用，我们需要做一些改进

1.  我们希望有一些可恢复的方法来解析更多的东西，而不仅仅是数字。
2.  我们还需要一些可重复使用的方法来创建类似于`plusExpr`中的序列。现在，对解析器进行排序涉及到一些样板文件:

*   在每一步，我们必须检查结果是否是一个错误，以决定我们是否应该继续或停止
*   我们还需要将输入的其余部分传递给下一个解析器

这似乎不算多。但是请记住，在实践中，我们会多次创建这种序列。所以以某种方式抽象出来会让我们的生活变得更容易。

所以首先(1)。我们将创建几个创建解析器的辅助函数。

第一个只会生成一个解析器来解析给定的字符串

```
function text(match) {
  return function textParser(input) {
    if (input.startsWith(match)) {
      return success(match, input.slice(match.length));
    }
    return failure(`'${match}'`, input);
  };
}

// example
const plus = text("+"); 
```

```
$ parse(plus, "+12")
  >> {data: "+", rest: "12"}

$ parse(plus, "12+")
  >> Uncaught Error: Parse error.
        expected '+'.
        instead found '12+' 
```

我们的第二个助手像第一个助手一样工作，但是匹配正则表达式而不是普通文本

```
function regex(regex) {
  const anchoredRegex = new RegExp(`^${regex.source}`);

  return function regexParser(input) {
    const match = anchoredRegex.exec(input);
    if (match != null) {
      const matchedText = match[0];
      return success(matchedText, input.slice(matchedText.length));
    }
    return failure(regex, input);
  };
}

const decimal = regex(/\d+(?:\.\d+)?/); 
```

```
parse(decimal, "12.34")
  >> {data: "12.34", rest: ""} 
```

嗯（表示踌躇等）...不完全是。我们的目标是一个实际的数字 2.3，而不仅仅是它的文本表示。

我们不能责怪我们的正则表达式助手。正则表达式可以用来解析任意数据类型，它不知道我们期望的是什么类型的数据。因此，我们需要一些通用的方法将文本表示转换成一些有意义的数据。

为了使它更加“通用”,我们将定义另一个帮助函数，它转换任何解析器的结果，而不仅仅是正则表达式的结果。满足`map`功能

```
function map(func, parser) {
  return function mapParser(input) {
    const result = parser(input);
    if (result.isFailure) return result;
    return success(func(result.data), result.rest);
  };
}

const decimal = map(x => +x, regex(/\d+(?:\.\d+)?/)); 
```

```
$ parse(decimal, "12.34")
  >> {data: 12.34, rest: ""}

$ parse(decimal, "a12.34")
  >> Uncaught Error: Parse error.
        expected /\d+(?:\.\d+)?/.
        instead found 'a12.34' 
```

肯定不是最有用的错误信息。我们稍后会看到如何改进这一点。

既然我们已经定义了我们的*原语*解析器。让我们定义我们的排序组合子。

我们已经知道，我们的序列器需要处理步骤之间的**错误处理**和**状态传递**(即传递输入的其余部分)。最后一个问题是:返回值应该是什么？

可能有多个答案

1.  我们可以只返回上一步的结果
2.  我们也可以返回一个包含所有步骤结果的数组
3.  我们可以将一些给定的函数应用于所有步骤的结果，并返回结果

如果我们考虑一下，我们可以根据(3)来定义(1)和(2)(另一种可能性是将(2)与`map`一起使用，但我们将坚持使用(3))。

好的。所以我们的组合子将接受两个参数:

1.  将应用于从所有解析器收集的结果的函数
2.  要排序的解析器数组

```
function apply(func, parsers) {
  return function applyParser(input) {
    const accData = [];
    let currentInput = input;

    for (const parser of parsers) {
      const result = parser(currentInput);
      if (result.isFailure) return result;
      accData.push(result.data);
      currentInput = result.rest;
    }

    return success(func(...accData), currentInput);
  };
} 
```

我们的`plusExpr`解析器现在可以用 apply
来定义

```
const plusExpr = apply((num1, _, num2) => num1 + num2, [
  decimal,
  plus,
  decimal
]); 
```

```
$ parse(plusExpr, "12+34")
  >> {data: 46, rest: ""}

$ parse(plusExpr, "12+34rest")
  >> {data: 46, rest: "rest"} 
```

哎呀！我们忘记了处理输入的结尾。

没关系。我们将为那个
创建一个解析器

```
function eof(input) {
  if (input.length === 0) return success(null, input);
  return failure("end of input", input);
}

// fix plusExpr
const plusExpr = apply((num1, _, num2) => num1 + num2, [
  decimal,
  plus,
  decimal,
  eof
]); 
```

```
$ parse(plusExpr, "12+34rest")
  >> Uncaught Error: Parse error.
        expected end of input.
        instead found 'rest' 
```

使用`apply`我们可以为排序
的其他可能结果定义助手

```
// Yeah not the best name I guess
function sequence(...parsers) {
  return apply((...results) => results[results.length - 1], parsers);
} 
```

```
function collect(...parsers) {
  return apply((...results) => results, parsers);
} 
```

```
$ parse(
    sequence(text("hello"), text(", "), text("world")),
    "hello, world"
  )
  >> {data: "world", rest: ""}

$ parse(
    collect(text("hello"), text(", "), text("world")),
    "hello, world"
  )
  >> {data: ["hello", ", ", "world"], rest: ""} 
```

## 合并解析器

我们将通过允许更多算术运算来改进我们的表达式解析器。

我们需要修改`plusExpr`，这样在第二步中，它就可以处理除“+”之外的其他*选择*。

啊，像往常一样，我们需要我们的解决方案是通用的，这样我们就可以允许任意解析器之间的替换，而不仅仅是简单的字符串(所以你猜对了，简单的正则表达式不会做到这一点)。

你现在应该习惯了。我们需要另一个解析器组合子。

```
function oneOf(...parsers) {
  return function oneOfParser(input) {
    for (const parser of parsers) {
      const result = parser(input);
      if (result.isFailure) continue;
      return result;
    }
    // We'll see later a way to improve error reporting
    return failure("oneOf", input);
  };
} 
```

我们现在可以做一个更好的表达式解析器(和评估器)。

```
const opMap = {
  "+": (left, right) => left + right,
  "-": (left, right) => left - right,
  "*": (left, right) => left * right,
  "/": (left, right) => left / right
};

function getOp(op) {
  return opMap[op];
}

const op = map(getOp, oneOf(text("+"), text("-"), text("*"), text("/")));

const decimal = map(x => +x, regex(/\d+(?:\.\d+)?/));

const expr = apply((num1, opFunc, num2) => opFunc(num1, num2), [
  decimal,
  op,
  decimal
]); 
```

```
$ parse(expr, "12-34")
  >> {data: -22, rest: ""}

$ parse(expr, "12*34")
  >> {data: 408, rest: ""} 
```

很好。但是错误报告可以做得更好

```
$ parse(expr, "a12*34")

>> Uncaught Error: Parse error.
        expected /\d+(?:\.\d+)?/.
        instead found 'a12*34'

parse(expr, "12 + 34")
  >> Uncaught Error: Parse error.
        expected oneOf.
        instead found ' + 34' 
```

我们不再支持空白。

真实世界解析器的正确错误报告不仅仅包括打印正则表达式或`oneOf`解析器的友好名称。我们需要报告错误的精确位置(文件，行&列),以及在这个位置预期的所有替代(包括来自深度嵌套的解析器)。

我们~~将~~可能会在另一篇文章中更详细地介绍错误报告。目前，我们的解决方案将是一个简单的`label`助手，它用一条用户友好的消息来修饰给定的解析器。该实现有一些缺陷(更准确地说，我们需要修复 lookahead ),但足以满足我们当前的需求

```
function label(parser, expected) {
  return function labelParser(input) {
    const result = parser(input);
    if (result.isFailure) {
      // replace the parser error with our custom one
      return failure(expected, result.actual);
    }
    return result;
  };
}

const decimal = map(x => +x, label(regex(/\d+(?:\.\d+)?/), "a decimal"));

const expr = apply((num1, opFunc, num2) => opFunc(num1, num2), [
  decimal,
  label(op, "an arithmetic operator"),
  decimal
]); 
```

```
$ parse(expr, "12 + 34")
  >> Uncaught Error: Parse error.
        expected an arithmetic operator.
        instead found ' + 34'

$ parse(expr, "a12 + 34")
  >> Uncaught Error: Parse error.
        expected a decimal.
        instead found 'a12 + 34' 
```

我们最后要做的是跳过空格，让解析器更真实一点。

```
// lexeme is a function which takes a parser for 'junk' (eg whitespaces, comments)
function lexeme(junk) {
  // and returns another function which takes a parser for some meaningful data
  return function createTokenParser(parser) {
    // the (second) function returns a parser that
    // parses the menaninful data then skips the junk
    return apply((data, _) => data, [parser, junk]);
  };
}

const spaces = regex(/\s*/);
const token = lexeme(spaces);

// redefine our experssion to skip leading and trailing spaces
const expr = apply((_, num1, opFunc, num2) => opFunc(num1, num2), [
  spaces, // skips leading spaces
  token(decimal),
  token(label(op, "an arithmetic operator")),
  token(decimal), // skips trailing spaces
  eof
]); 
```

```
$ parse(expr, " 12 + 34 ")
  >> {data: 46, rest: ""} 
```

## 产生解析器

你们中的一些人可能知道，作为 [redux-saga](https://github.com/redux-saga/redux-saga)
的原作者，我对生成器情有独钟(一些 FP 的人视其为受限的 do 符号，但无论如何)。

想象一下，我们可以使用泛型来编写像`expr`这样的序列。我们可以写类似于
的东西来代替`apply`

```
const expr = go(function*() {
  yield spaces;
  const num1 = yield decimal;
  const opFunc = yield op;
  const num2 = yield decimal;
  yield eof;
  return opFunc(num1, num2);
}); 
```

yield 语句嵌入了所有的错误处理和状态传递机制。我们可以像调用普通函数一样编写序列。

实现`go`并不比实现`apply`花费更多的时间。唯一的区别是，我们不是遍历一个解析器数组，而是遍历一个生成器对象。生成器生成连续的解析器，最后返回一个值，该值将作为主解析器的最终结果返回。

```
function go(genFunc) {
  return function yieldParser(input) {
    const gen = genFunc();
    let currentInput = input;
    let genResult = gen.next();
    // if not done yet, genResult.value is the next parser
    while (!genResult.done) {
      const result = genResult.value(currentInput);
      if (result.isFailure) return result;
      currentInput = result.rest;
      genResult = gen.next(result.data);
    }
    // if done, genResult.value is the return value of the parser
    return success(genResult.value, currentInput);
  };
} 
```

`expr`的生成器定义看起来比基于`apply`的定义(也称为应用定义)更有命令性。有些人喜欢第一种风格，有些人喜欢第二种。生成器定义(又名一元定义)也允许一些应用程序无法实现的事情。例如，想象一下解析一个类似 html 的语法，其中每个开始标签必须有一个相应的结束标签

```
const openBracket = text("<");
const closeBracket = text(">");

const element = go(function*() {
  // parses opening tag
  yield openBracket;
  const tagName = yield identifier;
  yield closeBracket;
  yield whateverContent;
  yield text(`</${tagName}>`);
}); 
```

在最后一步，生成的解析器是动态创建的。在解析开始标记之前，没有办法知道什么将是结束标记。对于`apply`,所有的解析器必须被静态地传递(事先知道),所以我们不能有上述类型的定义。

生成器也允许一些不错的引用定义。例如，假设我们想尽可能多地解析某个令牌

```
$ parse(many(regex(/\d/)), "123xyz")
  should return >> {data: ["1", "2", "3"], rest: "xyz"} 
```

我们可以用这样的生成器来定义`many`

```
// creates a parser that always succeeds with `value` without consuming any input
function pure(value) {
  return function pureParser(input) {
    return success(value, input);
  };
}

function many(parser) {
  const self = oneOf(
    go(function*() {
      const head = yield parser;
      // 1\. keep calling self recursively
      const tail = yield self;
      return [head, ...tail];
    }),
    // 2\. until it fails in which case we return an empty array
    pure([])
  );
  return self;
} 
```

使用`many`我们可以解析任意长度的表达式

```
const expr = go(function*() {
  yield spaces;
  const num1 = yield decimal;
  const rest = yield many(collect(op, decimal));
  yield eof
  return rest.reduce((acc, [opFunc, num]) => opFunc(acc, num), num1)
}); 
```

```
$ parse(expr, '1 + 2 + 3 + 4')
  >> {data: 10, rest: ""} 
```

### 还有更多

一篇文章不可能详细介绍解析器组合子。对于那些想更进一步的人，我做了一个库 [pcomb](https://github.com/yelouafi/pcomb) ,它打包了一组更全面的组合子。它还没有准备好投入生产，但是已经有足够的特性可以和更高级的解析器一起使用了。包括一些解析器的例子，说明了组合子是如何工作的。

以下是仍然需要涉及的内容(可能会在以后的帖子中涉及)

*   前瞻:例如，我们的`oneOf`定义允许任意前瞻。这意味着即使一个替代在失败前消耗了任意数量的输入，`oneOf`将总是从当前输入的起点重新开始下一个替代。

这在实践中效率不高，并且不允许正确的错误报告。在实践中，我们可以更好地限制前瞻，以便在消耗一些输入的同时，如果当前选择失败，那么`oneOf`不会尝试另一个选择。这也将允许更好的错误报告，因为我们可以准确地传播特定位置的预期内容。

*   (适当的)错误报告，这包括报告失败的确切位置以及该位置的预期项目，同时仍然允许开发人员插入他们自己的错误消息。

*   用户状态:解析复杂语言涉及到状态登记(例如“我们在函数体内吗？”).这包括允许解析器读/写状态信息。最简单和可组合的解决方案是将状态读取器/写入器主题编写为可以插入序列中的解析器。

*   使用模块化接口重构:将错误处理和状态传递到 sparate 接口中(就像在 Haskell 中用 Monad Transformers 栈所做的那样)。这提供了一个更灵活的接口，允许开发者插入他们自己的实现。

我希望你喜欢这篇文章，也希望你在创建自己的解析器时会有一些乐趣。