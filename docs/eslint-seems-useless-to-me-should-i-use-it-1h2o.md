# ESLint 对我来说似乎毫无用处。该不该用？

> 原文：<https://dev.to/nsebhastian/eslint-seems-useless-to-me-should-i-use-it-1h2o>

[![How it this stuff useful again?](img/281d841b9782ac477ae9f06fc4558482.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AMz6SUme--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jv8mmasb5mf0ilw5q1lz.png)

兄弟，无意冒犯，但对我来说，ESLint 似乎没什么大不了的。我的产品代码已经在没有林挺的情况下运行了两年。为什么我现在要用一个？

首先，我想说这是真的。没有林挺，你也可以使用 JavaScript。事实上，没有林挺，你可以使用任何编程语言。每一个错误的语法都会触发一个错误，你必须在发布代码之前修复它。

然而从我的经验来看，linters 最有用的功能是它让应用看起来像是由一个人写的。每个开发人员都有自己的风格，使用 ESLint 意味着你有一个关于你的 JavaScript 代码库应该是什么样子的书面协议。

好的，我明白了。但是代码风格本身不足以说服我..

你有没有犯过忘记清理 console.log()的错误？ESLint 可以捕捉这些代码并返回错误。

嘿，既然你提到了，我有时也会这么做..

是的，有时候开发人员会因为整天看着他们的编辑器而筋疲力尽。小错误，但仍然需要修复。另外，有时我们会忘记删除*未使用的导入语句*。有一次，我导入了一个开发所需的 React 组件，但是当我完成这个任务时，我已经不再使用这个组件了。导入语句留在文件中，应该被删除。

*呜呜。有时候我也这样做..*

太好了。是的，ESLint 会让你看到那些讨厌的未使用的变量或导入。

关于 ESLint 的另一个有用之处是，它可以帮助您处理那些通过运行或查看代码很难捕捉到的东西。考虑这个例子。你能猜出代码有什么问题吗？

```
for (i = 10; i >= 5; i++) {
    console.log("The number is " + i );
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯..有什么不好？

有点难以看清，是吧？嗯，增量值在错误的方向上移动，导致无限循环。

啊，我的错！

你可以让 ESLint 告诉你循环永远不会结束，而不是去想代码有什么问题。它可以节省一些时间。

*LOL*

这里再看一个例子:

```
if(username = "Douglas"){
 // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

*那是赋值运算符，不是比较..*

是的，当你运行代码时，JavaScript 会返回 true，而不是对你大喊大叫。在需要比较运算符的条件语句中，不允许使用赋值运算符。

*哦..那是整洁的..我还是看过它的文档，似乎我需要在使用它之前配置很多东西。*

你总是为你的项目配置 Gulp 或 Webpack，不是吗？ESLint 配置并不难获得。如果你真的那么懒，你可以使用它的共享配置，你可以自由使用。

像样板规则一样，是吧？

可以，但是不要设置好了就忘了！你必须看看这个规则对这个项目是否有意义。比如这里有 [Google](https://github.com/google/eslint-config-google) 的 ES 风格指南。确保您首先看到了它的文档。

*叹息..有什么方法可以让我忘记过去吗？另一个需要维护的配置很麻烦。*

...嗯，有一个倡议是为高质量的 JavaScript 代码制定一个通用的规则，您只需安装并运行它，无需进行任何配置。叫做[标准](https://github.com/standard/standard)。标准中列出的规则是固定的，贡献者在规则回购的发行部分进行辩论。

*牛逼！我要试试那个！*

如果你决定使用它，你也可以使用文本编辑器插件。

好吧。少了一个需要考虑的配置！

你知道什么最有趣吗？

*什么？*

不知何故，我认为许多开发者都同意你的观点，ESLint 配置是一个负担。Standard 设法在 Github clean code linters collection 中夺得第一名。

不是所有的开发者都是一样的。大多数人肯定不喜欢必须配置一切的想法。也许除了你。

可能...🤓无论如何，不要忘记添加一个 *git 钩子*,以便 linter 总是在提交之前运行。