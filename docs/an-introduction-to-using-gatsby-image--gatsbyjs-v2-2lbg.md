# 盖茨比形象使用介绍& Gatsby.js V2

> 原文：<https://dev.to/changoman/an-introduction-to-using-gatsby-image--gatsbyjs-v2-2lbg>

*本帖原载于[codebushi.com](https://codebushi.com/using-gatsby-image/)T3
T5】获取视频教程，查看[https://www.youtube.com/watch?v=kOrohVsq_kI](https://www.youtube.com/watch?v=kOrohVsq_kI)T8】*

[![Gatsby Image](img/0e36b733799b0f35eca64fe4da37dc94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jjb9eFit--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q81b4am24ospos6snr2e.jpg)

Gatsby.js V2 最近推出，对[盖茨比形象](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-image)的实现方式做了一些小改动。Gatsby Image 是一个 React 组件，可以轻松优化网站上的所有图像。它会为你调整图像大小，这样你就不会在移动设备上加载巨大的图像，它还会用一种很酷的“模糊”效果来延迟加载你的图像，这样你的初始页面加载速度会非常快。如果你是《盖茨比》的新手，我强烈推荐你先去看看他们的[官方教程](https://www.gatsbyjs.org/tutorial/)，熟悉一下《盖茨比》是如何运作的。

将 Gatsby 图像添加到您的静态网站可能有点棘手，尤其是因为 Gatsby 使用 GraphQL 来查询和加载您的图像，然后才能使用它们。以下是所需步骤的分类:

1)安装所需的 npm 软件包并配置您的`gatsby-config.js`设置。

2)测试您是否可以使用 GraphQL 查询您的图像。

3)选择您需要的图像类型，固定或流动，并将查询添加到您的页面。

4)在你的页面上使用 Gatsby Image `<Img>`标签。

这是最终产品的演示:

#### [盖茨比形象演示](https://gatsby-image-v2.surge.sh/) <small>( [查看来源](https://github.com/codebushi/gatsby-image-v2) )</small>

### 安装和配置 Gatsby 映像

我们将从安装[默认的 Gatsby 启动器](https://github.com/gatsbyjs/gatsby-starter-default)开始。您可以克隆 repo 或使用 Gatsby CLI 来安装 starter。

```
gatsby new image-demo https://github.com/gatsbyjs/gatsby-starter-default
cd image-demo/ 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 CLI，您将需要继续使用`yarn`，因为最初的包是用`yarn`安装的，并且将会有一个 yarn.lock 文件。如果您克隆了 repo 并使用了`npm install`，那么继续使用`npm`，这样就不会混淆软件包安装程序。在接下来的演示中，我将使用`yarn`。

安装盖茨比镜像

```
yarn add gatsby-image 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要另外三个包，[盖茨比-transformer-sharp](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby-transformer-sharp) 、[盖茨比-plugin-sharp](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby-plugin-sharp) 和[盖茨比-源文件系统](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-source-filesystem)。如果您没有使用默认的启动程序，并且已经安装了这些软件包，那么您可以跳过这一步。

```
yarn add gatsby-transformer-sharp gatsby-plugin-sharp gatsby-source-filesystem 
```

Enter fullscreen mode Exit fullscreen mode

`gatsby-source-filesystem`包允许 Gatsby 对某个目录中的图像使用 GraphQL，并对它们进行查询。两个`sharp`插件在你显示图像之前处理它们。

打开你的`gatsby-config.js`并添加插件。我会将它们添加到现有插件之前。您的文件应该是这样的:

```
module.exports = {
  siteMetadata: {
    title: 'Gatsby Default Starter',
  },
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/images`,
        name: 'images',
      },
    },
    'gatsby-transformer-sharp',
    'gatsby-plugin-sharp',
    'gatsby-plugin-react-helmet',
    {
      resolve: `gatsby-plugin-manifest`,
      options: {
        name: 'gatsby-starter-default',
        short_name: 'starter',
        start_url: '/',
        background_color: '#663399',
        theme_color: '#663399',
        display: 'minimal-ui',
        icon: 'simg/gatsby-icon.png', // This path is relative to the root of the site.
      },
    },
    'gatsby-plugin-offline',
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示:**确保你给你的图片指定了正确的`path`！`gatsby-source-filesystem`将在该文件夹中查找以访问您的图像。因为我们使用默认的启动器，在`/src/images`已经有一个文件夹，所以我们将使用它。从 [Unsplash](https://unsplash.com/) 中获取一些图像，并将它们添加到该文件夹中。

### 用 GraphQL 测试图像查询

随着插件的安装，我们可以在开发模式下启动我们的网站。

```
gatsby develop 
```

Enter fullscreen mode Exit fullscreen mode

导航到`http://localhost:8000/`在开发模式下查看您的站点。现在我们将使用 GraphiQL 接口来理解图像查询是如何工作的。前往`http://localhost:8000/___graphql`查看网站的图形视图。在这里，我们可以测试可用的不同查询。我已经在我的`/src/images`文件夹中添加了 3 张图片，并将它们命名为`one.jpg` `two.jpg`和`three.jpg`。为了查询`one.jpg`，我将使用这个:

```
query  {  imageOne:  file(relativePath:  {eq:  "one.jpg"})  {  childImageSharp  {  fluid(maxWidth:  1000)  {  base64  tracedSVG  aspectRatio  src  srcSet  srcWebp  srcSetWebp  sizes  originalImg  originalName  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您点击播放按钮，您应该在响应栏中看到数据。这证明盖茨比能够找到你的形象并加以处理。

[![](img/069201f46d1a7d2f9744c3a7e48428f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fopHgD6W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.screencast.com/users/hunter1291/folders/Jing/media/3a554288-ac4b-4e35-92b3-7fd5b2024052/00000530.png)

尝试将`file(relativePath: {eq: "one.jpg"})`更改为该文件夹中的其他图像，并确保看到数据返回。

### 添加 GraphQL 查询

我们现在可以复制这个查询，并在我们的主页组件中使用它。打开`src/pages/index.js`。你需要从`'gatsby'`进口`graphql`，也需要从`'gatsby-image'`进口`Img`。我们将向页面添加查询，最终结果如下:

```
import React from 'react'
import { Link, graphql } from 'gatsby'
import Img from 'gatsby-image'

import Layout from '../components/layout'

const IndexPage = (props) => (
  <Layout>
    <h1>Hi people</h1>
    <p>Welcome to your new Gatsby site.</p>
    <p>Now go build something great.</p>
    <Link to="/page-2/">Go to page 2</Link>
  </Layout>
)

export default IndexPage

export const pageQuery = graphql`
  query {
    imageOne: file(relativePath: { eq: "one.jpg" }) {
      childImageSharp {
        fluid(maxWidth: 1000) {
          ...GatsbyImageSharpFluid
        }
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

这个查询看起来和以前有点不同，我们删除了`fluid(maxWidth: 1000) {}`中的所有字段，使用了`...GatsbyImageSharpFluid`，这是一个“查询片段”。由于一些限制，我们以前不能在 GraphiQL 中使用`...GatsbyImageSharpFluid`，但是我们可以在这里添加它。你可以在[盖茨比形象自述](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-image)上阅读更多关于不同片段的内容。

**重要提示:**注意`file(relativePath: { eq: "one.jpg" })`部分是如何保持不变的，这是因为`relativePath`不是相对于`index.js`的，而是你之前在`gatsby-config.js`和`gatsby-source-filesystem`中指定的文件夹。没有必要改变`relativePath`的任何东西。

盖茨比形象有两种反应型形象，`fixed`和`fluid`。这种区别会改变查询的外观。一个`fixed`查询有一个设定的宽度和高度，用于支持不同的*屏幕分辨率*。一个`fluid`查询有一个最大宽度，有时有一个最大高度，并将创建多个图像以支持不同的*屏幕尺寸*。在大多数情况下，我发现自己使用的是`fluid`类型，因为我的图像会随着屏幕的大小而变化。如果你想使用`fixed`类型或者希望了解更多关于这两种类型的信息，请查看[自述文件](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-image)。

### 使用 Gatsby 图像组件

所以我们在页面上有我们的查询，GraphQL 数据可以通过我们的`IndexPage`组件中的`props`来访问。数据的完整路径是`props.data.imageOne.childImageSharp.fluid`。我们可以像这样将它传递给`<Img>`组件:

```
<Img fluid={props.data.imageOne.childImageSharp.fluid} /> 
```

Enter fullscreen mode Exit fullscreen mode

你可以任意的破坏它，为了清楚起见，我使用了完整的路径。该图像现在应该显示在您的开发网站！要获得所有三个图像，只需复制并粘贴`imageOne`块，并重命名为`imageTwo`和`imageThree`。您可以随意调用它们，只要确保它与您传递给`<Img />`组件的内容相匹配。

```
query  {  imageOne:  file(relativePath:  {  eq:  "one.jpg"  })  {  childImageSharp  {  fluid(maxWidth:  1000)  {  ...GatsbyImageSharpFluid  }  }  }  imageTwo:  file(relativePath:  {  eq:  "two.jpg"  })  {  childImageSharp  {  fluid(maxWidth:  1000)  {  ...GatsbyImageSharpFluid  }  }  }  imageThree:  file(relativePath:  {  eq:  "three.jpg"  })  {  childImageSharp  {  fluid(maxWidth:  1000)  {  ...GatsbyImageSharpFluid  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

组件应该是这样的:

```
<Img fluid={props.data.imageOne.childImageSharp.fluid} />
<Img fluid={props.data.imageTwo.childImageSharp.fluid} />
<Img fluid={props.data.imageThree.childImageSharp.fluid} /> 
```

Enter fullscreen mode Exit fullscreen mode

我们在查询中重复了很多相同的东西，可以通过定制片段来清理。拉出`childImageSharp`块，像这样做一个新的片段:

```
export const fluidImage = graphql`
fragment fluidImage on File {
  childImageSharp {
    fluid(maxWidth: 1000) {
      ...GatsbyImageSharpFluid
    }
  }
}
`; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以用这个新的片段替换重复的代码，就像这样:

```
export const pageQuery = graphql`
  query {
    imageOne: file(relativePath: { eq: "one.jpg" }) {
      ...fluidImage
    }
    imageTwo: file(relativePath: { eq: "two.jpg" }) {
      ...fluidImage
    }
    imageThree: file(relativePath: { eq: "three.jpg" }) {
      ...fluidImage
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的主页上就有了这三张图片！你可以玩不同的盖茨比片段来获得不同的加载效果。`...GatsbyImageSharpFluid`将产生“模糊”效果，尝试`...GatsbyImageSharpFluid_tracedSVG`获得不同的效果，并尝试固定图像。

#### [盖茨比形象演示](https://gatsby-image-v2.surge.sh/) <small>( [查看来源](https://github.com/codebushi/gatsby-image-v2) )</small>