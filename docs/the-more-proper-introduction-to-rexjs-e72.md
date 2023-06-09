# ReX.js 更恰当的介绍

> 原文：<https://dev.to/areknawo/the-more-proper-introduction-to-rexjs-e72>

# 对于新手来说

ReX.js(正则表达式的一个快捷方式)是一个用于(显然)创建正则表达式的库。通过它的函数式 API，您可以用一组可链接的方法轻松地构建 regexps。它允许你构建复杂而长的正则表达式，具有更好的可读性&可以轻松地在旁边写注释。让我给你看一个取自回购的“README”文件的小例子:

```
import { Matcher } from '@areknawo/rex'

const expr = new Matcher()
.find('Reg')
.whitespace()
.capture((expr) => {
  expr.find('Exp')
}).test('Reg Exp'); //true 
```

Enter fullscreen mode Exit fullscreen mode

# 是开玩笑吗？

在你对我大喊为什么不写点这样的东西之前:

```
/Reg Exp/.test('Reg Exp') // true 
```

Enter fullscreen mode Exit fullscreen mode

请稍等片刻。正如我所写的，ReX.js 是为了更快更容易地创建复杂的 regexps。如果你不同意我的观点，那么这个怎么样:

```
// IP Address verification regexp taken from https://regexr.com
// Long regex coming!
        /\b(?:(?:2(?:[0-4][0-9]|5[0-5])|[0-1]?[0-9]?[0-9])\.){3}(?:(?:2([0-4][0-9]|5[0-5])|[0-1]?[0-9]?[0-9]))\b/ig 
```

Enter fullscreen mode Exit fullscreen mode

这个创作的可靠性怎么样？只是请-不要错过这一点 regexp 是非常棒和非常有用的工具，它绝对值得学习，而不是 ReX.js 的整个 API。rex . js 的目的不是取代 regexp。它的目的是为那些已经了解 regexps 语法的人提供工具，以获得更好的开发体验。这就是为什么 ReX.js 方法直接以某些 regexp 构造的名称命名。所以让我们用 ReX.js 重写上面的 regexp monstrosity！

```
import { Matcher } from '@areknawo/rex'

const expr = new Matcher()
  .wordBoundary()
  .capture((expr) => {
    expr
      .capture((expr) => {
        expr
          .find('2')
          .capture((expr) => {
            expr
              .range(0, 4)
              .range(0, 9).or('5')
              .range(0, 5)
          }).negate() // negate method is just negating last operation
          .or((expr) => {
            expr.range(0, 1)
          }).optional()
          .range(0, 9).optional()
          .range(0, 9)
      }).negate() // so it means just a non-capturing group (logical)
      .find('.')
  }).negate() // negate also works with other operations that can be negated
  .quantify(3, 3)
  .capture((expr) => {
    expr
      .capture((expr) => {
        expr
          .find('2')
          .capture((expr) => {
            expr
              .range(0, 4)
              .range(0, 9).or('5')
              .range(0, 5)
          }).or((expr) => {
            expr.range(0, 1)
          }).optional()
          .range(0, 9).optional()
          .range(0, 9)
      })
  })
  .wordBoundary()
  .i().g();
expr.test('123.123.123.256') // false
expr.test('123.123.123.123') // true 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。这实际上很长，但是可读性更好(至少在我看来)。请记住，上面的创建只是给定正则表达式的文字拷贝。使用 ReX.js API 可以缩短一点。

# 好吧...我很感兴趣

这还不是全部 ReX.js 是用 TypeScript 编写的(我知道这是误导人的后缀),所以它为所有方法提供了编辑器内自动完成功能。

# 最终

如果我能让你对 ReX.js 感兴趣，请随时查看官方[文档](https://areknawo.github.io/Rex)、 [Github Repo](https://github.com/areknawo/rex) 和 [Codesandbox playground](https://codesandbox.io/s/jnp66xzlw3) ！