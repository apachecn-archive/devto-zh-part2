# 与盖茨比一起轻松创建自己的博客

> 原文：<https://dev.to/wonism/create-your-own-blog-with-gatsby-45hh>

[Gatsby](https://www.gatsbyjs.org/) 是一个静态站点生成器。(像 Jekyll(Ruby)、Hexo(Node.js)、Hugo(Go)等生成器种类实在太多了。)盖茨比支持 PWA(渐进式 Web App)，热重装，SSR(服务器端渲染)。

你可以在[这个环节](https://www.gatsbyjs.org/features/)看到更详细的内容。

## 安装盖茨比 CLI 和盖茨比启动器

```
$ npm i -g gatsby-cli
$ gatsby new <<BLOG_NAME>> https://github.com/wonism/gatsby-advanced-blog
$ cd <<BLOG_NAME>>
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

## 项目的基本结构

该项目具有以下结构。

```
src/
├── components
│   ├── ... # Many of shared sections
│   └── layout.jsx # general layout for page
├── constants
├── containers # to connect states to react component
├── html.jsx # page template for page
├── pages # pages of your web site
│   ├── 404.jsx
│   └── index.js
├── postComponents # react application that will be added in page
│   └── ...
├── resources # asset files
│   └── images
├── store # to use redux
│   ├── ...
│   └── index.js
├── templates # template for creating page with file system
│   └── ...
└── utils # utilities
    └── ... 
```

Enter fullscreen mode Exit fullscreen mode

## 创建帖子

```
$ mkdir src/pages/<<DIRECTORY_NAME>>
$ touch src/pages/<<DIRECTORY_NAME>>/index.md 
```

Enter fullscreen mode Exit fullscreen mode

这些降价文件由`gatsby-source-filesystem`引用，并由`gatsby-transformer-remark`转换成 HTML 文件。

这些都是在建造时调用的。你可以在`gatsby-node.js`里查看这个`createPages`。

### 小心

`<<PROJECT_ROOT>>/src/pages`中有一个`.sample.md`文件。
如果删除这个文件，就无法获得`category`、`image`等。在 GraphQL 的`frontmatter`中查询。

`.sample.md`文件作为虚拟数据，创建自定义`frontmatter`字段。

### 降价文件的基本组成部分

```
--------
path: "/hello-world/"
category: "Sample"
tags: ["tag", "should", "be", "array"]
title: "Hello, World!"
date: "2018-08-15T00:00:00.000Z"
summary: "You can create your own blog with Gatsby!"
-------- 
Content of this page 
```

Enter fullscreen mode Exit fullscreen mode

*   `path`是创建页面的一个**必需的**属性。必须是独一无二的。
*   `category`允许您创建类别页面并访问类似`/categories/<<CATEGORY_NAME>>/<<PAGE_NUMBER>>`的页面。
*   `tags`允许您创建标签页面和访问类似`/tags/<<TAG_NAME>>/<<PAGE_NUMBER>>`的页面。
*   正如你所看到的物业名称。`title`是页面的标题，`summary`是页面的摘要。
*   `date`是文章创建的日期，文章根据它进行排序。

(你可以在[灰质](https://github.com/jonschlinkert/gray-matter)中获得更多关于格式化的详细信息。)

这些将由`src/templates/Post.jsx`中的`query`调用。并且可以直接在[http://localhost:8000/_ _ _ graph QL](http://localhost:8000/___graphql)上运行查询

### 添加图片到帖子中

```
images: ["(<<src/resources/>>)PATH_TO/IMAGE"] 
```

Enter fullscreen mode Exit fullscreen mode

使用包含`http://`或`https://`的绝对路径。或者使用相对于`src/resources`的相对路径。
(在`components/Post/index.jsx`的第 145 行，图像通过`IF CONDITION ? <<image>> : '<<src/resources/${image}>>'`导入)

### 将 react 应用添加到帖子中

```
--------
path: "/inject-app/"
category: "Sample"
tags: ["tag", "must", "be", "array"]
title: "Injecting React application"
date: "2018-08-15T00:00:00.000Z"
summary: "You can inject react application into post"
components: [{
  rootId: 'sample-component', # <div id="sample-component"></div> must be in contents
  fileName: 'sample', # this will render src/postComponents/sample/index.jsx
}]
--------  1. ...

<div id="sample-component"></div>
 2. ... 
```

Enter fullscreen mode Exit fullscreen mode

将一个对象放入数组。对象必须包含要添加的 react 应用程序文件的路径和要呈现的标记的 ID。

然后，在文章中间，在想要插入 react 应用程序的地方添加带有这个 ID 的标签。

### 在帖子中添加推文

```
--------
path: "/inject-tweet/"
category: "Sample"
tags: ["tag", "must", "be", "array"]
title: "Injecting Tweet"
date: "2018-08-15T00:00:00.000Z"
summary: "You can inject tweet into post"
tweets: [{
  rootId: 'sample-tweet', # <div id="sample-tweet"></div> must be in contents
  userId: 'twitter', # twitter user id
  tweetId: '977557540199456775', # tweet id
}]
--------  1. ...

<div id="sample-tweet"></div>
 2. ... 
```

Enter fullscreen mode Exit fullscreen mode

将一个对象放入数组。对象必须包含 tweet 的 ID 和作者，或者 tweet 和要呈现的标签的 ID。

您可以像添加 react 应用程序一样使用它。

### 在帖子中添加代码

如果您在想要突出显示的代码之前和之后写了三个反引号，它将被`gatsby-remark-prismjs`突出显示。

## 添加投资组合

```
$ mkdir src/resources/<<DIRECTORY_NAME>>
$ touch src/resources/<<DIRECTORY_NAME>>/index.md 
```

Enter fullscreen mode Exit fullscreen mode

```
--------
type: "portfolio"
title: "Gatsby Advanced Blog"
date: "2018-08-15T00:00:00.000Z"
path: "/portfolios/portfolio-1/"
images: [
  "test-1/1.png",
  "test-1/2.png",
]
-------- 
# Gatsby Advanced Blog

## What I did
- Develop Gatsby Advanced Blog

## Libraries / Tools
- ReactJS
- Redux
- Redux saga
- ...

[Go to Web Site →](https://github.com/wonism/gatsby-advanced-blog) 
```

Enter fullscreen mode Exit fullscreen mode

*   `path`是创建页面的一个**必需的**属性。必须是独一无二的。
*   `type`是一个允许你指定页面格式的值，这里应该是`portfolio`。
*   `title`是作品集的标题。
*   `images`是您想要附加到作品集的图片，并且与文章具有相同的价值。图像按照添加到数组中的顺序呈现。
*   投资组合根据`data`排序。给投资组合一个更大的价值来首先展示。

投资组合将显示在投资组合页面。如果超过 4 个，就会显示在主页上。

(你可以在`src/components/Home`中看到它，并修改它的渲染方式。)

## 添加简历

```
--------
type: "resume"
title: "Resume"
date: "2000-01-01T00:00:00.000Z"
path: "/resume/"
-------- 
## Experience
- Engineer at OOO ∙ 2000\. 01 ~ Present
 - Develop something
 - Maintain something

## Education
- B.S. in Computer Science Engineering at OOO
 - 2000\. 01 ~ 2000\. 01

## Projects
- Gatsby Advanced Blog (https://github.com/wonism/gatsby-advanced-blog) ∙ 2000\. 01 ~ Present
 - Integrate Redux
 - Use Redux, Redux Saga, Reselect...

## Skills
- JavaScript
 - ES2015+
 - ReactJS
 - Lodash
- CSS
 - SASS
 - Less 
```

Enter fullscreen mode Exit fullscreen mode

*   `path`是创建页面的一个**必需的**属性。必须是独一无二的。
*   `type`是一个允许你指定页面格式的值，这里应该是`portfolio`。

## 其他特性

*   在 GNB 的右边，你可以通过标题、摘要、标签、类别等来搜索文章。的职位。
*   当您在 markdown 中添加代码时，会自动创建一个按钮。如果用户点击按钮，用户可以复制代码。

## 部署

你可以用`npm run build`来构建应用。
然后你可以把它分发到你想分发的地方。如 [Github 页面](https://pages.github.com/)、 [AWS S3](https://aws.amazon.com/s3) 或 [Netlify](https://www.netlify.com/) 。

你可以在这个[链接](https://wonism.github.io/create-blog-with-gatsby-en/)上看到这篇文章。