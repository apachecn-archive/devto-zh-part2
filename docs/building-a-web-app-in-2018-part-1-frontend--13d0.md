# 2018 年构建 web 应用，第 1 部分:前端

> 原文：<https://dev.to/rhymes/building-a-web-app-in-2018-part-1-frontend--13d0>

这篇文章或多或少是我在 2017 年 11 月发表的这篇文章的后续:

[![rhymes](img/91ed8188209dceb0374b6a8c88308333.png)](/rhymes) [## 2017 年如何打造 web app？

### 押韵 11 月 22 日 173 分钟阅读

#webdev #discuss](/rhymes/how-to-build-a-web-app-in-2017-a6m)

这款应用，或者至少是它的 MVP，现在还活着(有缺陷)，已经投入生产几周了。

在之前的帖子中，我问了很多问题，在这篇帖子中，我将尝试提供一些答案。请记住，我花了几个月的时间进行“现代”前端开发，但并没有成为专家。

# 这是什么 app？

不幸的是，我不能透露太多关于“什么”的细节，但它几乎是一个在 AWS S3 上发布静态 HTML 页面的 web 应用程序。(公司)用户输入数据，神奇的事情发生了，手机应用程序上的客户看到了手机应用程序内的迷你网站。没什么了不起的，但有一些移动的部分，未来会出现更多，将被控制或集成在应用程序本身中，现在由单独的工具或应用程序处理。

# 这个 app 是怎么架构的？

在之前的帖子中，在网上摄取了大量疯狂的内容后，这让我非常困惑，我问这个应用程序是否应该是一个 SPA。我在这里告诉你，我的第一个也是唯一一个 SPA 正在工作:-)

一般来说，它是一个用 VueJS 构建的 SPA，其后端是一个 Python/Flask web 服务器。

这两个部分位于同一个存储库中，它们只是位于两个独立的目录中:

```
$ ls -1 */
backend/:
README.md
TODO.md
app
business
config
coverage
docs
forms
jobs.py
migrations
models.py
package.json
services
setup.cfg
tests

frontend/:
README.md
TODO.md
build
config
dist
index.html
node_modules
package.json
src
static
test yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

在开发模式下，前端使用 [webpack 的 DevServer 代理](https://webpack.js.org/configuration/dev-server/#devserver-proxy)与后端对话，在生产模式下，前端构建在`frontend/dist`文件夹中，后端知道这个文件夹是它的静态文件夹。

我不确定后者是最好的想法，但目前它工作得很好，尽管有时我很想把前端移到后端，因为它们在技术上是同一个应用程序。我还没决定。

一切都部署在赫罗库。

# 关于前端的一些事情

正如你可能想象的那样，前端是最让我担心的部分，因为对我来说，不构建传统的服务器端应用程序并在需要 JavaScript 时使用 jQuery 是一件新鲜事。即使当您试图正确构建 jQuery 代码时，仍然感觉像是事后诸葛亮。

我被 Vue 吸引的原因可能和我多年前被 Python 吸引的原因一样:我不喜欢复杂的东西。我喜欢能够处理复杂问题的简单技术。React 并没有吸引我太多(我对失败者情有独钟，在生活中也是:D)，JSX 对我来说没有意义，我爱上了 Vue 中的“单文件组件”风格，我确信它可以做我需要的一切。

经过反复试验，我使用
[vue“web pack”模板](https://github.com/vuejs-templates/webpack)正确地创建了我的`frontend`文件夹。

这个模板可以完成你所需要的一切:设置基本的应用程序结构，基本的命令，配置 babel 及其插件，eslint，所有的 webpack 构建工具，与后端的集成非常容易设置，它还可以设置单元和 e2e 测试。

你只需要适应它，而不是反对它，尤其是像我一样，当你第一次创建应用程序时，你会对生成的代码数量感到犹豫。生成的代码你根本不知道它有什么用。我希望 Webpack 4 和零配置方法将在未来改善这个部门的情况。

## 构建前端的框架和库

首先，我试图决定使用哪种 UI 框架。Vue 有这么多，对一个新人来说决定起来不太容易。客户给我完全的技术自由并没有真正的帮助(约束是有用的)。我最后的犹豫是在 Boostrap Vue 和 Buefy 之间，Buefy 是一个基于 CSS 框架布尔玛的框架。

我诚实地选择了 [Buefy](https://buefy.github.io/) 因为我的眼睛已经厌倦了看到影印的基于 Bootstrap 的网站(包括我自己的网站),并且不是我能成为的前沿艺术家，我选择了新奇。我不是很理性，但别担心，我检查了项目是否还在进行，他们是否有我需要的主要组件。

我正在使用的其他 UI 库有:用于 Buefy 材质设计图标的 [mdi](https://yarnpkg.com/en/package/mdi) 、令人惊叹的文件上传工具 [uppy](https://uppy.io/) 、用于将文本复制到用户剪贴板的 [v-clipboard](https://yarnpkg.com/en/package/v-clipboard) 、用于表单验证的 [vee-validate](https://yarnpkg.com/en/package/vee-validate) 、用于颜色选择器的 [vue-color](https://yarnpkg.com/en/package/vue-color) 。

我还使用 [axios](https://yarnpkg.com/en/package/axios) 调用后端、 [luxon](https://yarnpkg.com/en/package/luxon) 操纵时间、 [js-file-download](https://yarnpkg.com/en/package/js-file-download) 触发文件下载、 [sentry 的 js 客户端](https://yarnpkg.com/en/package/raven-js)收集和报告错误。

最后但同样重要的是: [vue-router](https://router.vuejs.org/en/) 在前端处理路由。

正如你所想象的，它不是最灵活的应用程序，但它是一个内部应用程序，它只在桌面上使用，我现在没有优化任何东西，只是建立和学习:-)

## 帮助人类构建前端的框架和库

除了 VSCode 和显而易见的 webpack，我还使用了其他工具:

*   [vue 测试工具](https://vue-test-utils.vuejs.org/)、 [axios 模拟适配器](https://github.com/ctimmerm/axios-mock-adapter)和 [flush promises](https://yarnpkg.com/en/package/flush-promises) 正在帮助我在 [Jest](https://facebook.github.io/jest/) 中编写单元测试

*   eslint 和 T2 style lint 有助于保持代码的整洁。对于 JS，我使用带有分号的 airbnb 风格，但是你也可以在初始模板创建时选择“标准”。对于 CSS，我同时使用 CSS 和 SASS/SCSS，所以 stylelint 知道后者。

## 偷窥前端的 package.json

这是我的`package.json` :
的`scripts`部分

```
 "scripts":  {  "analyze":  "webpack --config build/webpack.prod.conf.js --profile --json > /tmp/stats.json && webpack-bundle-analyzer /tmp/stats.json",  "build":  "node build/build.js",  "dev":  "SERVER_ENV=development webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",  "e2e":  "node test/e2e/runner.js",  "lint-css":  "stylelint 'src/**/*.vue'",  "lint-fix":  "yarn run lint-js-fix",  "lint-js-fix":  "eslint --report-unused-disable-directives --color --ext .js,.vue src test/unit/specs test/e2e/specs --fix",  "lint-js":  "eslint --report-unused-disable-directives --color --ext .js,.vue src test/unit/specs test/e2e/specs",  "lint":  "yarn run lint-css && yarn run lint-js",  "start-dashboard":  "SERVER_ENV=development webpack-dashboard -- webpack-dev-server --config build/webpack.dev.conf.js",  "start":  "yarn run dev",  "test":  "yarn run unit",  "unit":  "jest --config test/unit/jest.conf.js --coverage"  }, 
```

Enter fullscreen mode Exit fullscreen mode

有些已经被模板设置好了，有些是我的，有些像`start-dashboard`我不确定为什么还在那里:-D

# 我在构建这个前端的过程中学到了什么(和没学到什么)

如果你还和我在一起，我想分享一些我学到的常识:

*   这不是最容易配置的东西。有时候，你最终会在 Github 上阅读关于问题的评论，看看如何解决这个或那个问题。

*   现代前端开发肯定比过去容易。是的，学习 Vue 有一点学习曲线，但工具很神奇，webpack 的热加载器很神奇(我真的不知道所有这些东西是如何工作的，我不是 100%确定我关心)

*   现代前端开发对开发者来说更快。你引入了一个 bug，在编辑器中修复它，按下⌘-tab，修复就已经在页面上了(VSCode 被配置为在失去焦点时保存)。与后端的交互也是如此:我在前端保存一个表单，后端崩溃是因为它在某些功能上有错误，我编写服务器端单元测试，修复代码，在前端再次按 save，就这样。没有刷新，没有重新输入数据的形式，没有什么。

*   对于用户来说，现代前端开发可能不会更快。用户不知道也不关心你如何构建你的应用程序，所有这些 JavaScript 让我有点害怕。不是因为技术本身，而是因为它的庞大。用[代码分割](https://webpack.js.org/guides/code-splitting/)在运行时加载组件有一点帮助，但是我还没有掌握所有的东西。

*   在 Vue 中构建可重用的组件非常简单:基本上你只需要接受一个`value`并发出一个`input`事件，然后你就可以使用 [v-model](https://vuejs.org/v2/guide/components.html#Form-Input-Components-using-Custom-Events) 将数据从子节点发送到父节点。我会在以后的文章中详细讨论。

*   我还是没有掌握 Vue 中的代码复用。我有一些可重用的组件，一些混合组件(主要用于验证),但是我仍然有许多不同组件之间的代码重复。我并不担心，我确信我了解得越多(尤其是关于[槽](https://vuejs.org/v2/guide/components.html#Content-Distribution-with-Slots))它就会消失

*   我还是不明白[嵌套路由](https://router.vuejs.org/en/essentials/nested-routes.html)是如何工作的。我的路由现在在路由器内部都是逻辑平坦的，只是路径是嵌套的。

*   我没有使用 [vuex](https://vuex.vuejs.org/en/intro.html) 。这并不是说我不知道它的用途，我可能会在某个时候需要它，但现在每个组件都有自己的状态，可重用的组件使用`v-model`与它们的父组件对话，主要组件的状态来自创建时的后端。它负责初始化自己的每个组件，要么通过它自己的状态(想想“创建新的东西”的形式)，要么通过一个 API 调用。

# 结论

老实说，我认为如果我选择了没有 Vue 的“安全和铺平的道路”,我会花更多的时间来发布这个应用程序。

我要在这里停下来，因为这篇文章已经很长了。我还没有谈到可重用组件、测试和后端。期待未来:D 更多的文章