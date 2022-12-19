# 如何编写简单的 babel 宏

> 原文：<https://dev.to/stereobooster/how-to-write-simple-babel-macro-1al1>

宏是一个小程序，你可以编写它来在编译时操作你的应用程序的源代码。可以把它看作是调整编译器行为的一种方式。

`babel-plugin-macros`是 babel 的一个插件，为 JavaScript(或 Flow)写宏。有趣的是，一旦包含了`babel-plugin-macros`，你就不需要触摸 babel config 来使用你的宏了(与其他 babel 插件相反)。这在锁定设置中非常有用，比如 Creat React App。此外，我喜欢它是显式的——你可以清楚地看到宏在哪里被使用。

## 任务

我拿起玩具大小的问题，这是很容易解决的宏。

当你在 Webpack 中使用 dynamic `import`的时候，它会为程序块生成硬可读的名字(至少在 CRA 是这样的)，比如`1.chunk.js`、`2.chunk.js`。要解决这个问题，你可以使用魔法注释`/* webpackChunkName: MyComponent */`，这样你就会得到`MyComponent.chunk.js`，但是每次都要手动添加这个注释，这很烦人。让我们编写 babel 宏来解决这个问题。

我们想要这样的代码:

```
import wcImport from "webpack-comment-import.macro";

const asyncModule = wcImport("./MyComponent"); 
```

Enter fullscreen mode Exit fullscreen mode

要转换为

```
const asyncModule = import(/* webpackChunkName: MyComponent */ "./MyComponent"); 
```

Enter fullscreen mode Exit fullscreen mode

## 样板文件

所以我想直接跳到编码，所以我不会花时间在样板文件上。有一个 GitHub repo，标签为[`boilerplate`](https://github.com/stereobooster/webpack-comment-import.macro/tree/boilerplate)，可以看到初始代码。

```
export default createMacro(webpackCommentImportMacros);
function webpackCommentImportMacros({ references, state, babel }) {
  // lets walk through all calls of the macro
  references.default.map(referencePath => {
    // check if it is call expression e.g. someFunction("blah-blah")
    if (referencePath.parentPath.type === "CallExpression") {
      // call our macro
      requireWebpackCommentImport({ referencePath, state, babel });
    } else {
      // fail otherwise
      throw new Error(
        `This is not supported: \`${referencePath
          .findParent(babel.types.isExpression)
          .getSource()}\`. Please see the webpack-comment-import.macro documentation`,
      );
    }
  });
}
function requireWebpackCommentImport({ referencePath, state, babel }) {
  // Our macro which we need to implement
} 
```

Enter fullscreen mode Exit fullscreen mode

还配置了测试和构建脚本。我不是从头开始写的。我是从 [raw.macro](https://github.com/pveyes/raw.macro) 复制过来的。

## 咱们码吧

首先得到 [`babel.types`](https://www.npmjs.com/package/babel-types) 。事情是这样的:当你使用宏时，你所做的主要是操作 AST(源代码的表示)，而`babel.types`包含了对 babel AST 中使用的所有可能类型的表达式的引用。 **[`babel.types`自述](https://www.npmjs.com/package/babel-types)如果你想和巴别塔 AST 合作，是最有帮助的参考。**T9】

```
function requireWebpackCommentImport({ referencePath, state, babel }) {
  const t = babel.types; 
```

Enter fullscreen mode Exit fullscreen mode

`referencePath`是来自`const asyncModule = wcImport("./MyComponent");`的`wcImport`，所以我们需要获得更高的级别，以实际调用函数，例如`wcImport("./MyComponent")`。

```
 const callExpressionPath = referencePath.parentPath;
  let webpackCommentImportPath; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以得到调用我们函数的参数，为了确保没有有趣的事情发生，让我们使用`try/catch`。函数调用的第一个参数假定是导入的路径，例如`"./MyComponent"`。

```
 try {
    webpackCommentImportPath = callExpressionPath.get("arguments")[0].evaluate()
      .value;
  } catch (err) {
    // swallow error, print better error below
  }

  if (webpackCommentImportPath === undefined) {
    throw new Error(
      `There was a problem evaluating the value of the argument for the code: ${callExpressionPath.getSource()}. ` +
        `If the value is dynamic, please make sure that its value is statically deterministic.`,
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后是 AST 操作——让我们用`import("./MyComponent");`、
代替`wcImport("./MyComponent")`

```
 referencePath.parentPath.replaceWith(
    t.callExpression(t.identifier("import"), [
      t.stringLiteral(webpackCommentImportPath),
    ]),
  ); 
```

Enter fullscreen mode Exit fullscreen mode

让我们得到路径的最后一部分，例如将`a/b/c`转换为`c`。

```
 const webpackCommentImportPathParts = webpackCommentImportPath.split("/");
  const identifier =
    webpackCommentImportPathParts[webpackCommentImportPathParts.length - 1]; 
```

Enter fullscreen mode Exit fullscreen mode

并将魔术组件放在导入的第一个参数之前:

```
 referencePath.parentPath
    .get("arguments")[0]
    .addComment("leading", ` webpackChunkName: ${identifier} `);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是了。我尽量保持简短。我没有跳到很多细节，问问题。

## PS

Babel 文档有点难，对我来说最简单的方法是:

1.  用`console.log(referencePath.parentPath.type)`检查表达式的类型，并在`babel.types`中读取
2.  阅读其他做类似事情的巴别塔插件的源代码

完整源代码在[这里](https://github.com/stereobooster/webpack-comment-import.macro)

希望有帮助。试试看。告诉我进展如何。或者简单地分享你们巴别塔宏的想法。

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。