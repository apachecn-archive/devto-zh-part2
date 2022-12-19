# ES6 的即插即用测试

> 原文：<https://dev.to/gmartigny/plug-and-play-tests-for-es6-4ei9>

这篇文章是继我上一篇关于模块化图书馆建筑的文章之后发表的，但是你不必先阅读它。

如果你在这里，我敢打赌你知道自动化测试的重要性。

我想找到测试浏览器模块化库的最简单明了的方法，所以下面是我的发现。

# 测试服

首先，你需要一套测试服。外面有很多！喜欢，很多！！让我们回顾一下其中的几个，以及我对每一个的看法。

## [摩卡](https://mochajs.org/) -灵活

Mocha 是最受欢迎的浏览器，但在我看来有两个缺点:

*   没有内置的断言库
*   与他人相比啰嗦

## -后起之秀

老实说，我喜欢 AVA。它有一个很好的语法，运行测试非常快。唯一缺陷是它只支持节点，需要额外的步骤(和配置)来处理浏览器 JS。

## [俏皮话](https://facebook.github.io/jest/) -得心应手

我找到了满足我们需求的最佳解决方案。
清晰的语法，易于设置，快速，干净的输出...
顶部的樱桃是有[这个 webpack-jest 包](https://github.com/mzgoddard/jest-webpack)为你设置好一切。

# [的用法是](#use-jest)

这一段会很短。

首先，添加一些开发依赖项。

```
npm install --save-dev jest jest-webpack 
```

Enter fullscreen mode Exit fullscreen mode

然后，将“测试”脚本添加到 package.json 文件中。

```
"name":  "myAwesomePackage",  "scripts":  {  "test":  "jest-webpack"  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，运行“测试”脚本。

```
npm test 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

# 写测试

默认情况下，jest 会抓取所有以`.test.js`、`.spec.js`结尾的文件，或者你喜欢的`__tests__`目录下的任何文件。

我不打算描述整个交易，但这个笑话很容易理解。让我给你看一个有意义的例子。

```
import Unicorn from "./myAwesomePackage.js";
import somethingElse from "AnotherPackage";

// Wrap this test suit
describe("Unicorn", () => {

    // Start every tests with a clean state
    let sparkle;
    beforeAll(() => {
        sparkle = new Unicorn("Sparkle");
    });

    // Some test
    test("instantiate", () => {
        // Simple assertion
        expect(sparkle instanceof Unicorn).toBe(true);
        expect(sparkle.name).toBe("Sparkle");
    });

    // More test
    test("shine", => {
        // String assertion
        expect(sparkle.shine()).toMatch(/🦄/);
        expect(sparkle.shine()).not.toMatch(/💩/);
    });

    // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

现在，您应该能够通过很少的设置来测试您的 ES6 代码。👍

现在，去尝试覆盖你的代码库。但是请记住，[更聪明的测试比大量的测试更好。](https://www.developerdotstar.com/mag/articles/test_smarter_not_harder.html)

如果你想检查一个真实的例子，我在我的图片库项目中使用了完全相同的技术。