# 安培⚡使用盖茨比

> 原文：<https://dev.to/tomoyukikashiro/amp--using-gatsby-4kmo>

**这篇文章最初发表在 [AMP ⚡，使用的是盖茨比](https://blog.tomoyukikashiro.me/post/amp-using-gatsby)。**

我创建了 gatsby 插件(名为 [gatsby-plugin-html2amp](https://github.com/tomoyukikashiro/gatsby-plugin-html2amp) )用于生成 amp(加速移动页面)。我试着解释如何使用它。

它很容易使用😁

## 准备盖茨比博客

```
$ npm install --global gatsby-cli
$ gatsby new gatsby-blog https://github.com/gatsbyjs/gatsby-starter-blog 
```

然后查看博客

```
$ cd gatsby-blog
$ npm start

# Access http://localhost:8000 
```

## 做成 AMP！

### 添加插件

```
$ npm install --save gatsby-plugin-html2amp 
```

将插件配置设置为文件底部的`gatsby-config.js`。

```
{
  resolve: 'gatsby-plugin-html2amp',
  options: {
    files: ['**/*.html'],
    dist: 'public/amp'
  }
} 
```

### 修改博文模板

要使你的帖子页有效，请在`<head>`中添加`canonical`

***src/templates/blog-post . js*T3** 

```
export const pageQuery = graphql`
  query BlogPostBySlug($slug: String!) {
    site {
      siteMetadata {
        title
        author
      }
    }
    markdownRemark(fields: { slug: { eq: $slug } }) {
      id
      excerpt
      html
      fields { // ⚡ Add this fields.slug into Graphql
        slug
      }
      frontmatter {
        title
        date(formatString: "MMMM DD, YYYY")
      }
    }
  }
` 
```

然后添加 canonical

***src/templates/blog-post . js*T3** 

```
<Helmet
  htmlAttributes={{ lang: 'en' }}
  meta={[{ name: 'description', content: siteDescription }]}
  title={`${post.frontmatter.title} | ${siteTitle}`}>
  <link rel="canonical" href={`${post.fields.slug}`} /> // ⚡ Add canonical
</Helmet> 
```

### 生成

```
$ npm run build 
```

现在你可以在⚡看到放大器源