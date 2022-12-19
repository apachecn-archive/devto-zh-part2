# 使用 Flexbox 的 GatsbyJS 中的粘性页脚

> 原文：<https://dev.to/mokkapps/sticky-footer-in-gatsbyjs-using-flexbox-5162>

我最近开发了一些基于 [GatsbyJS](https://gatsbyjs.org) 的静态网站，它们有一个粘性页脚。粘性页脚总是位于页面的底部，即使对于稀疏的内容也是如此。

不幸的是，我很难解决这个问题，因此，我想与你分享我的经验。

## 非盖茨比方案

在传统的 HTML、CSS、JavaScript 应用程序中，我们可以使用不同的方式来实现这样一个固定的页脚，但我更喜欢 Philip Walton 的 Flexbox 解决方案(T2)。

Flexbox 为粘性页脚问题提供了一个很好的解决方案。它可以用来在水平和垂直方向布局内容。所以我们只需要将垂直部分(页眉、内容、页脚)包装在一个 flex 容器中，并选择哪一个应该展开。在我们的例子中，我们希望内容自动占据容器中的所有可用空间。

下面，你可以看到他的解决方案:

```
<body class="site">
  <header>…</header>
  <main class="site-content">…</main>
  <footer>…</footer>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

对应的 CSS 类:

```
.site {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

.site-content {
  flex: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

看一下[现场演示](https://philipwalton.github.io/solved-by-flexbox/demos/sticky-footer/)。

## 盖茨比方案

盖茨比基于 React，因此，我们必须有不同的想法。

来自[官方 GatsbyJS 默认启动器](https://github.com/gatsbyjs/gatsby-starter-default)的基本`layout.js`文件具有类似如下示例的结构:

```
const Layout = ({ children }) => (
  <StaticQuery
    query={graphql`
      query SiteTitleQuery {
        site {
          siteMetadata {
            title
          }
        }
      }
    `}
    render={data => (
      <>
        <Helmet
          title={data.site.siteMetadata.title}
          meta={[
            { name: 'description', content: 'Sample' },
            { name: 'keywords', content: 'sample, something' },
          ]}
        >
          <html lang="en" />
        </Helmet>
        <Header siteTitle={data.site.siteMetadata.title} />
        <div>{children}</div>
        <Footer />
      </>
    )}
  /> );

export default Layout; 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果我们按照菲利普·沃顿在 T2 的解决方案 T3 中提出的方式来设计 T0 和 T1，它将无法工作。

但是为什么呢？因为这意味着`<Footer/>`组件将包含在`<body></body>`中。

为了解决这个问题，我添加了一个新的`<div></div>`标签，它相当于上面提到的例子中的`<body></body>`标签。

所以我的`layout.js`看起来是这样的:

```
const Layout = ({ children }) => (
  <StaticQuery
    query={graphql`
      query SiteTitleQuery {
        site {
          siteMetadata {
            title
          }
        }
      }
    `}
    render={data => (
      <>
        <Helmet
          title={data.site.siteMetadata.title}
          meta={[
            { name: 'description', content: 'Sample' },
            { name: 'keywords', content: 'sample, something' },
          ]}
        >
          <html lang="en" />
        </Helmet>
        <div className="site">
          <Header siteTitle={data.site.siteMetadata.title} />
          <div className="site-content">{children}</div>
          <Footer />
        </div>
      </>
    )}
  /> );

export default Layout; 
```

Enter fullscreen mode Exit fullscreen mode

CSS:

```
.site {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

.site-content {
  flex-grow: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在我的 [GitHub 流量查看器网站](https://github-traffic-viewer.netlify.com/)上看到一个工作示例。第一页显示了一个备用内容，但页脚粘在底部。如果您登录并看到结果列表，页脚也会显示在页面底部。

[![Example Website](../Images/fd05f4b521ca1311831865b2ea3fb362.png)T2】](///static/example-d8fc28e05d95bb213da4f3b47f119e04-55f2e.png)

如果你试图在一个 GatsbyJS 网站中实现一个粘性页脚，我希望这篇文章对你也有帮助。

编码快乐！