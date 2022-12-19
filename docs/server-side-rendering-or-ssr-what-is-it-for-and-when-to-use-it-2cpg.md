# 什么是 Ssr 服务器端渲染利弊。什么时候用，什么时候选别的

> 原文：<https://dev.to/stereobooster/server-side-rendering-or-ssr-what-is-it-for-and-when-to-use-it-2cpg>

> Daniel H. Tong 在 Unsplash 上拍摄的照片

## 什么是 SSR，为什么要关心？

SSR 代表服务器端渲染。我将主要谈论 React，但我想它对其他框架也有意义。

如果您关心以下问题，您需要 SSR:

*   **第一次有意义的绘画**。单靠 SSR 并不能保证好的结果。你还需要关键的 CSS 和接近客户等。
*   搜索引擎优化和支持其他机器人，如推特和脸书
*   **优雅的退化**。对于这一点，您需要确保您的服务没有 JS 也是可用的

## 有什么难的？

SSR 就像一个新的维度。不管你用什么，你都需要为 SSR 重新配置它。

*   你使用`componentDidMount`来获取数据吗？你需要使用类似于`getInitialProps`(来自 next.js 或者 after.js)的东西或者像 Redux 这样的状态管理库来让它在服务器上工作
*   你用路由器吗？您需要在服务器上配置它
*   你用 i18n 吗？您需要在服务器上配置它
*   你使用 HMR 吗？您将需要为浏览器和服务器重新加载代码
*   你用反应头盔吗？您需要在服务器上配置它
*   你用 react-loadable 吗？您需要配置服务器来传递使用过的模块，以便客户端可以预加载它们
*   你用 Redux 吗？您需要序列化存储并将其传递给客户端
*   你使用 CSS-in-JS 吗？您需要对其进行配置，以便在服务器上生成关键的 CSS，并将其内联到 HTML 响应中

不要误解我，这都是可以解决的。Next.js 和 Razzle 解决了大部分问题。我想向你展示的是 SSR 是如何让一切(另一个维度)翻倍的，而且大多数时候都需要样板文件。

好的。现在让我们来看看福利。

## 第一次意味深长的描绘

如果你在做 SSR，这并不意味着你会从盒子里得到好的第一幅有意义的画。

*   你的设置对第一个字节有帮助吗？如果你的服务器运行缓慢或过载，你就会有问题。确保使用最新的节点，缩小服务器代码，[使用流渲染](https://zeit.co/blog/streaming-server-rendering-at-spectrum)，优化子查询(数据库或网络)，如果有的话。
*   你提供关键的 CSS 吗？否则浏览器无法开始渲染页面。
*   你使用网络字体吗？如果是，你[优化它](https://www.zachleat.com/web/comprehensive-webfonts/)了吗？
*   你的服务器离客户端近吗？**如果你的服务器在欧洲，但客户端在日本，SSR 在这里不会帮你**。可以发生的是，从 CDN 服务“壳”会比做 SSR 更快(从印象的角度来看)。如果由于法律限制，您不能将服务器移动到离客户机更近的地方，该怎么办？
*   你确定你没有不必要的重定向吗？否则，在慢 3G 上重定向会毁了你好不容易获得的毫秒。

SSR 不是第一个有意义的绘画的银弹。如果你的后端很慢或者很远，你需要检查一下“shell”和 CDN 是否会更好。您可以使用类似于 [react-snap](https://github.com/stereobooster/react-snap) 的东西来预呈现静态页面，并为其他页面生成一个“外壳”。

如果你的网站倾向于更加静态，你可以使用预渲染来代替 SSR。检查出[反应-静态](https://react-static.js.org/)或[盖茨比](https://www.gatsbyjs.org/)或[反应-抓拍](https://github.com/stereobooster/react-snap)。

## SEO

这里有 3 个选项:

*   苏维埃社会主义共和国
*   预渲染，像反应快照，反应静态，盖茨比等。
*   根据需求进行预渲染，如 rendertron、puppetron、pupperender 等。

如果可以，选择预渲染。如果你唯一关心的是搜索引擎优化，在需求上预先呈现是很容易添加的。

## 优雅的退化

这个比较棘手。这真的取决于你想达到多大程度的退化？

*   你想支持链接吗？这应该有用
*   你想支持下拉菜单吗，就像他们在[https://www.seek.com.au/](https://www.seek.com.au/)做的那样？你需要对 CSS 和复选框使用一些技巧
*   你想要支持表格吗？除了现有的 JSON API 之外，您还需要端点来处理这些表单

一些没有 JS 的功能很难实现，比如组合框或者地图

## 结论

SSR 不错，试试吧。此外，确保你的用户确实从中受益。
如果你不能使用 SSR 尝试预呈现器，有时这是最简单的选择。

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。