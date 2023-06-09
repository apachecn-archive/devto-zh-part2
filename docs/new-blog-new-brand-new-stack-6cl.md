# 新博客、新品牌、新堆栈

> 原文：<https://dev.to/whoisryosuke/new-blog-new-brand-new-stack-6cl>

我的新个人博客和作品集已经上线。而且是用 GatsbyJS 做的静态 ReactJS 渐进式 web 应用！让我给你分析一下。

## 但是为什么要设计一个新的网站呢？

新的个人品牌=展示它的新网站。

我想用我的头脑来思考盖茨比的框架。几个月前，当我在为我的 [Stay Regular](http://stayregular.net) 工作室和[weeppordaily](http://weedporndaily.com)博客寻找静态解决方案时，我几乎没有这方面的经验。在切换到 Slate 之前，我最终将它用于 [Kushy](http://kushy.net) 文档(尽管我可能会切换回来，因为我更喜欢 JS 后端而不是 Ruby)。自从我第一次使用它以来，它们已经走过了很长的路——从改进的文档到我渴望探索的插件宝库。

## PWA 或半身像

我的目标是创建一个网站，它是*(主要是)* **静态的**，分类为 [**渐进式网络应用**或 PWA](https://developers.google.com/web/progressive-web-apps/) ，从 [Git 库](https://github.com/whoisryosuke/ryosuke-gatsby-blog/)部署，并且**仅由 CDN** 提供服务。我的所有内容都是在构建过程中从 API 预加载的，并硬编码到 HTML 中。只有像注释这样的东西会从远程 API 加载，因为系统需要一个服务器端应用程序来处理认证请求(我在 Heroku 上节省了时间)。

> 编译时，这个网站纯粹是 HTML，CSS，和 JS。

我能够完成所有这些都要感谢 GatsbyJS 框架的魔力。它充当一种静态站点生成器，从 T2 的 ReactJS 组件和 T4 的 graph QL API 构建静态站点。GatsbyJS 简化了打包一个完整的服务器端呈现的静态 React 应用程序，而不是像 [next.js](https://github.com/zeit/next.js/) 那样处理 SSR *(仍然没有路由分割或 Webpack 设置)*。

> GatsbyJS 框架处理从 webpack 捆绑到路由分割，再到服务器端呈现的所有事情。

## 精选

我来自 [Wordpress](http://wordpress.org) 的世界，所以我试着让这个博客尽可能的功能齐全。GatsbyJS 框架为你提供了一个 2 页的样板 *Hello World* 风格的应用程序，就是这样。其他一切都需要插件或自定义代码。所有这些特性都添加在基本的 GatsbyJS 特性之上:

*   **一键部署**——我只需运行`npm run deploy`，网站就构建好了，并从我的本地机器上传到 Github 页面。
*   我所有的内容都是用 Markdown 写的，必要时还会加入一些 HTML。
*   **语法突出显示** -代码由 PrismJS 着色，并使用 Gatsby Prism 插件集成。
*   **响应图像** -网站上的所有图像都响应用户的浏览器大小，为更小的视窗提供更小的文件，使网站超级数据高效。
*   **标签** -我有一个[标签页面](http://whoisryosuke.com/tags)，在这里你可以浏览每篇文章的所有标签，点击每个标签会带你去标签库(更多搜索引擎优化点！).
*   分页档案库(Paginated Archives)-如果用户和机器人无法浏览你的整个内容库，你就不可能有合适的 SEO。我可以用一个插件和一些魔法来完成它。
*   **注释** -这些由 Disqus 及其 React 组件处理。
*   RSS 提要(RSS Feed)——你不可能有一个没有 RSS 提要的博客，让人们直接输入他们的大脑，而不是使用你辛苦了几个星期的界面。这也通过一个插件变得非常简单。
*   **SEO 优化**——从服务器端渲染，到使用`react-helmet`抽取结构化数据，再到我安装的网站地图——这个网站已经为谷歌做好了准备。

唯一缺少的是 CMS ，但是我不能使用 Github 页面，我必须使用像 [Netlify](http://netlify.com) 这样的服务来处理自动构建。

## 设计

我为品牌设定风格标准，选择字体和颜色。我想要轻便、现代、有图案的东西——灵感来自日本设计。蓝色是最主要的颜色，因为它代表我。然后，我尝试创建组件来举例说明设计系统的方向。大量使用留白、圆角边缘和微妙的渐变来营造柔和的空间。大胆的图形图像和颜色会打破白色墙壁的单调。

以下是我在素描几轮后得出的结论:

[![Ryosuke page mockup in Sketch](img/657cad8c43ea3384925b9435e6a0f63b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QMKDYgAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/ryosuke-blog-page-ca5145bfbcbec15697acf28e70b9d2e6-e2462.png)
[![Ryosuke blog mockup in Sketch](img/f3f45b3b3bba0a9eab98e97a4775eed3.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--n-NKKg58--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/ryosuke-blog-posts-a45ebde64edf44705893ca61f9537904-e2462.png)

## 发展

盖茨比医生棒极了。我能做的大部分事情在这里都有很好的概述。虽然有一些技术我不得不真正搜索才能在 Github 问题或 StackOverflow 答案中找到。

我在文档中找不到如何在一个页面上完成多个 GraphQL 查询。GatsbyJS 只允许在主布局模板中进行 GraphQL 查询，而不是在单个组件中。每个人都指向 GraphQL 文档，说要使用片段，但是 GraphQL 文档只将片段描述为获取查询字段的一种方式——而不是整个查询。最后，我在 Github 上发现有人有类似的问题，有人展示了如何在一个页面上定义多个 GraphQL 查询:

```
export const query = graphql`
  query IndexQuery {
    blog: allMarkdownRemark(
      sort: {fields: [frontmatter___date], order: DESC}, 
      limit: 3
      filter:{frontmatter:{section:{eq: "blog"}}}
    ) {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
            cover_image {
              publicURL
              childImageSharp {
                sizes(maxWidth: 1240 ) {
                  srcSet
                }
              }
            }
            section
          }
          fields {
            slug
          }
          excerpt
        }
      }
    },
    projects: allMarkdownRemark(
      sort: {fields: [frontmatter___date], order: DESC}, 
      limit: 3
      filter:{frontmatter:{section:{eq: "project"}}}
    ) {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
            cover_image {
              publicURL
              childImageSharp {
                sizes(maxWidth: 1240 ) {
                  srcSet
                }
              }
            }
            section
          }
          fields {
            slug
          }
        }
      }
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

我还遇到了一个问题，就是如何使用 GatsbyJS `createPage`方法为`gatsby-node.js`文件中的节点生成静态页面。在文档中，他们描述了通过调用 promise 来使用该函数，该 promise 查询 GraphQL 中的数据，然后将这些数据转换为静态页面(就像个人博客帖子一样)。然而，我需要在不同的端点上运行多个查询，比如博客和项目。这个是我在 Github 上观察某人的 GatsbyJS 网站想出来的。最后，我在`createPage`方法中使用了回调函数，并向回调函数传递了查询 GraphQL:
所需的变量

```
/**
 *  Pagination for /blog/ page
 */
function createBlogPagination(graphql, createPage, resolve, reject) {
        graphql(`
      {
        allMarkdownRemark(
        filter:{frontmatter:{section:{eq: "blog"}}}
        ) {
            totalCount
            edges {
                node {
                id
                }
            }
        }
      }
    `).then(result => {

                createPaginatedPages({
                    edges: result.data.allMarkdownRemark.edges,
                    createPage: createPage,
                    pageTemplate: "src/templates/blog-archive.js",
                    pageLength: 6,
                    pathPrefix: "blog",
                    buildPath: (index, pathPrefix) => index > 1 ? `${pathPrefix}/${index}` : `/${pathPrefix}` // This is optional and this is the default
                });

            })
}

/**
 *  Create slug pages for markdown files
 *  Create pages for each tag
 */
exports.createPages = ({ graphql, boundActionCreators }) => {
    const { createPage } = boundActionCreators
    return new Promise((resolve, reject) => {
        graphql(`
      {
        allMarkdownRemark {
          edges {
            node {
                excerpt
                }
          }
        }
      }
    `).then(result => {
                /**
                 * Create blog posts based on slugs
                 */
                result.data.allMarkdownRemark.edges.forEach(({ node }) => {

                    // Grab random tag to do related posts
                    var tag = node.frontmatter.tags[Math.floor(Math.random() * node.frontmatter.tags.length)];

                    createPage({
                        path: node.fields.slug,
                        component: path.resolve(`./src/templates/blog-post.js`),
                        context: {
                            // Data passed to context is available in page queries as GraphQL variables.
                            tag: tag,
                            slug: node.fields.slug,
                        },
                    })
                });

                resolve()
            })
        createBlogPagination(graphql, createPage, resolve, reject);
        createProjectsPagination(graphql, createPage, resolve, reject);
    })
}; 
```

Enter fullscreen mode Exit fullscreen mode

## JAMstacks 是未来

随着我对全栈 web 开发趋势的观察，我发现越来越多的公司正在讨论转向基于微服务的架构，在这种架构中，客户端应用程序针对不同的功能与几个不同的 API 进行对话。根据应用程序的不同，将更多数据从 API 预加载到 CDN 变得更加容易，从而实现更高效的全球内容流。甚至像 [Smashing Magazine](http://smashingmagazine.com) 这样的公司已经证明[可以创建带认证的静态网站，或者电子商务购物车。](https://www.youtube.com/watch?v=_3zYAMkaMf8)。

我很高兴探索 JAMstacks 的世界，并推动像 GatsbyJS 这样的框架的功能。

## 感谢所有的鱼

感谢 GatsbyJS 创建了牛逼的框架，感谢 Github 免费托管我的回购和网站(包括一个自定义域！).

干杯，奥斯卡

* * *

**继续阅读:**

*   [盖茨比](http://gatsbyjs.org)
*   [JAM Stack:Web 开发的新前端堆栈](https://www.youtube.com/watch?v=_3zYAMkaMf8)
*   [盖茨比](http://gatsbyjs.org)