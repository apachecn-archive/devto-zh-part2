# 创作节点模块

> 原文：<https://dev.to/hemanth/authoring-node-modules-3k8a>

一个[矿](https://h3manth.com)的老[帖](http://h3manth.com/new/blog/2015/authoring-node-modules/)一个朋友建议重新贴在这里。

到目前为止，我已经在`npm`上创作/贡献了大约 [200+](https://www.npmjs.com/~hemanth) 个模块，它们每个月有大约`1.5M`次下载，这是一个不错的数字；作为其中的一部分，我学到了一些东西，我想在这篇文章中分享。

**编写模块前的待办事项:**

*   搜索 npm，寻找一个已经存在的模块来解决你的问题:你可以做一个`npm search <module>`或者从[站点](https://www.npmjs.com/)搜索

*   如果你找到一个这样好的模块，一定要阅读它的代码，看看你是否能改进一些东西，可能是性能、逻辑或测试修正。

*   如果您发现一个模块，它并不完全符合您的要求，那么发送一个`PR`来进行您希望的更改。

**写一个整洁的包:**

*   如果你是一个聪明懒惰的开发者，你可能想使用[生成器节点](https://github.com/yeoman/generator-node)。如果这对你来说太重了，那就检查一下[发电机-nm](https://github.com/sindresorhus/generator-nm)

*   如果你不想要`generators`，你可以继续使用`npm init`并手动创建所需的目录结构。

*   确保你的`package.json`是有效的，也许你需要 [package-json-validator](https://www.npmjs.com/package/package-json-validator)

*   如果您不熟悉`npm`，您可以使用[validate-npm-package-name](https://www.npmjs.com/package/validate-npm-package-name)来检查您的包名是否是有效的 NPM 包名。

*   如果你能确保你的`package.json`有一个`files`属性，而这个属性又有一个[主](https://docs.npmjs.com/files/package.json#main)属性，那就太好了。

*   确保你有合适的测试用例，并将其与持续构建服务集成，比如 [travis](https://travis-ci.org/) 。

*   使用徽章，如: [nodei](https://nodei.co/) ， [david](https://david-dm.org/) 等

*   确保你的`README.md`看起来不错，因为它有关于如何安装你的模块的信息，它做什么，API 文档和 gif，标志是一个加号。

*   使用 [release-it](https://www.npmjs.com/package/release-it) 等工具释放包装时要小心

**登顶:**

有很多方法可以展示你的模块，这样人们会发现它，并希望使用它，但这里有几个好方法来创建一个有用的包:

*   深入研究 GitHub，找到几个热门项目，阅读源代码，找到可以提取到模块中的代码，制作一个模块，并发送一个 PR，大多数情况下，模块化总是获胜。

*   深入到 node 的[最依赖的](https://www.npmjs.com/browse/depended)模块，重复上述步骤。

*   如果上面的方法对你没有帮助，制作你自己的轻量级模块，它只是大模块的一小部分。

页（page 的缩写）斯:这不仅仅是为了达到顶峰，而是为了做一件事，并以最好的方式去做。

**延伸阅读:**

*   我坚信应该编写单行节点模块，但是我想从 [@sindresorhus](https://twitter.com/sindresorhus) 那里听到同样的话，所以我向他提了一个[问题](https://github.com/sindresorhus/ama/issues/10)。

*   我和我的几个朋友([斯托菲尔](http://stoeffel.github.io/) & [托梅克维](https://github.com/tomekwi))一起写了[的俏皮话](https://github.com/1-liners/1-liners)，也检查了[的 npm 脚本](https://github.com/npm-scripts/scripts)

*   除了我提到的`readme.md`之外，看看这个[模板](https://gist.github.com/jxson/1784669)。