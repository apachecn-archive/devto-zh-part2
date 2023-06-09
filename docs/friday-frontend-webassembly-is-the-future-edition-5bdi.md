# 星期五前端:WebAssembly 是未来的版本

> 原文：<https://dev.to/kball/friday-frontend-webassembly-is-the-future-edition-5bdi>

这个星期让我非常兴奋和受启发的是网络大会。是的，我们仍处于早期，但这将为网络带来许多惊人的事情！

[https://www.youtube.com/embed/KZRyVo4Pr0I](https://www.youtube.com/embed/KZRyVo4Pr0I)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [我们如何和为什么单元测试我们的 Sass](https://seesparkbox.com/foundry/how_and_why_we_unit_test_our_sass)

使 CSS 成为一种乐趣的最大进步之一是开始分解成混合和函数，让你从可重用的功能块而不是一堆乱七八糟的东西的角度来考虑它。然而，一旦您开始重用代码，您必须始终小心，一个领域中的更改不会意外地破坏另一个领域。因此，单元测试！

##### [初学者的边界元法:为什么需要边界元法](https://www.smashingmagazine.com/2018/06/bem-for-beginners/)

我花了很长时间才理解 BEM 作为一名老派的开发人员，并且很久以前就已经是后台出身，它看起来过于冗长和复杂。一旦我跨越了最初的印象，我就很高兴在各种各样的项目中使用 BEM。我欣赏这篇文章，因为它不仅解释了 BEM 是什么，而且解释了许多选择背后的推理。如果我在第一次学习 BEM 的时候就拥有这些，我可能不会花这么长时间来决定使用它。

##### [热门提示 CSS](https://equinsuocha.io/blog/hot-tips-css/)

一堆超级快速的片段来实现不同的结果。您可以将此作为参考，也可以将其作为探索一些新特性的起点。我绕了很长一段路去调查 css 属性。

##### [哈里·罗伯茨:FaCSSt——CSS 和 Performance](https://www.youtube.com/watch?v=2Rn8an74khk)

这是一个关于 CSS 性能的精彩演讲。您知道吗，移动电话仅解析一个 1M 的样式表就需要 200 毫秒。在此期间，浏览器不能呈现任何东西？这还不包括关键路径中的下载时间。也许将图像内联到 CSS 中并不是一个好主意...许多其他真正有趣且有时不直观的性能在这里胜出。

##### [用 CSS 渐变绘制图像](https://css-tricks.com/drawing-images-with-css-gradients/)

首先，我得说我喜欢将 CSS 作为艺术表达的媒介，而不仅仅是完成工作的工具。用代码(各种各样的)创造性地做事让我对软件感到兴奋；当然，构建工具支付账单，但这才是乐趣所在！有了这些，这篇文章是非凡的——它不仅表达了你可以用 CSS 做什么，而且是一个超级详细的一步一步的教程，你可以用 CSS 画出漂亮的雨伞、iPhone，甚至是一个动画雷达。

#### JavaScript

##### [在网络上使用 JavaScript 模块](https://developers.google.com/web/fundamentals/primers/modules)

对于那些哀叹构建工具的增长，渴望通过脚本标签包含 JavaScript 文件的简单日子，但又想利用 JavaScript 的模块化优势的人来说，你很幸运！现在所有主流浏览器都支持原生 JavaScript 模块！它们的性能不如捆绑代码，所以如果你正在使用捆绑器，就坚持使用它，但是如果你真的想回到平面文件，你可以看看这个。:)

##### [引入单元素模式](https://medium.freecodecamp.org/introducing-the-single-element-pattern-dfbd2c295c5d)

简单、可组合的 React 组件的设计模式。我不确定我 100%同意这里的所有选择，但总的来说，我认为这些是很好的指导方针。这与我写的一篇关于 Vue 中的[透明包装组件的文章有很多重叠，但在这种情况下，React focused 认为这种模式应该应用于所有组件。](https://dev.to/kball/transparent-wrapper-components-in-vue-hdh)

##### [Vue.js:好的，坏的，丑的](https://medium.com/@Pier/vue-js-the-good-the-meh-and-the-ugly-82800bbe6684)

我认为这是对 Vue 中好与不好的一个很好的、平衡的评估。反应性中所有边缘情况的复杂性对我来说无疑是一个挑战，他准确地指出了问题在哪里得到回答以及需要一套记录良好的架构模式的挑战。

##### [你应该知道的 10 种 JavaScript 数组方法](https://dev.to/frugencefidel/10-javascript-array-methods-you-should-know-4lk3)

即使一直掌握 JavaScript，我也完全没有意识到我们现在可以在数组上使用像`reduce`和`some`这样的东西，而不需要像 lodash 这样的库。如果您正在使用 lodash 或其他一些数组实用程序，或者您还不太熟悉数组转换，请通读一遍。你不会后悔的。

##### [ECMAScript 装饰者最小指南](https://itnext.io/a-minimal-guide-to-ecmascript-decorators-55b70338215e)

装饰器是一种有趣的语言特性，我在 python 中经常使用，尽管我知道它们也有 TypeScript 支持，并且在 Angular 中大量使用。它们在历史上并不是 JavaScript 的一部分，但是它们正在规范过程中前进，通过巴别塔的魔力，如果你想的话，今天你可以在 JS 中使用它们！这是对它们是什么以及它们如何工作的一个很好的介绍。

#### 其他牛气

##### [Web assembly 如何加速 Web 开发的未来](https://dev.to/kball/how-webassembly-is-accelerating-the-future-of-web-development-mnp)

(免责声明——这是我写的)。随着我对 WebAssembly 的思考越来越多，我对它越来越感兴趣。甚至不一定是因为我想使用编译成 WASM 的语言——我很喜欢 JavaScript——而是因为我认为我们将能够在框架和库中使用它来获得一些显著的性能改进，在并行性和首次交互时间方面都是如此。

##### [在 Airbnb 反应原生](https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)

这是一个相当大的新闻——一家在 React Native 上押了很大赌注的大公司决定离开它。听起来平台和生态系统的成熟度是最大的决定因素，还有一些额外的注意事项，包括不同移动平台之间 JavaScript 环境的不一致性。他们仍然有很多好的东西要说——我的感觉是，如果你是一个迁移到 native 的 web 开发人员，React Native 仍然是一个不错的选择。但是如果你能负担得起专门为本地人组建团队的费用，那么完全的本地化可能是值得的。

##### [用 SVG 动画书法](https://css-tricks.com/animate-calligraphy-with-svg/)

这是另一个例子，既说明了一些令人惊叹的艺术作品是如何使用网络工具作为媒介的，也说明了 SVG 是多么令人惊叹。(对于那些还不知道的人，我有点爱上 SVG 了——我甚至[在它上面录制了一个简短的课程](https://skl.sh/2E8WWtH))用书法制作动画，就好像有人真的用笔在你的屏幕上画出来一样。太棒了。

##### [改进了 EdgeHTML 17 中的 JavaScript 和 WebAssembly 性能](https://blogs.windows.com/msedgedev/2018/06/19/improved-javascript-webassembly-performance-edgehtml-17/)

这似乎主要是增量的，所以我通常不会包括它，但这里有一些真正有趣的东西引起了我的注意。Edge 17 通过 get this 将 array `for...of`迭代的速度提高了 2.5 倍——从 C++重写为 JavaScript！JavaScript 编译器正变得越来越好。其他一些有趣的注意事项包括将一些 AngularJS 工作负载提高了 25%的优化！这是巨大的！看起来尽管取得了很大的进步，JS 的性能仍然有很大的提升空间。

##### [GraphQL，了解何时使用 JavaScript，以及 JavaScript robotics](https://changelog.com/jsparty/31)

(免责声明——我也参与其中)两周前在 Fluent Conf 上， [Tim Smith](https://twitter.com/smithtimmytim) 和我采访了十几位不同的演讲者，和他们谈论了他们的演讲和专业领域。这是其中 3 次对话的录音(也是那些喜欢阅读的人的抄本),涵盖了 GraphQL、CSS 以及何时使用它与 JavaScript、Typescript、JavaScript 中的机器人等主题。很多好东西！

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】