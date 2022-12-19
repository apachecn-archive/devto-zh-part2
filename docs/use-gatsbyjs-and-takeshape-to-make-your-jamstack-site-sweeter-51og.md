# 使用 Gatsby.js 和 TakeShape 使您 JAMstack 网站更可爱

> 原文：<https://dev.to/takeshape/use-gatsbyjs-and-takeshape-to-make-your-jamstack-site-sweeter-51og>

[![TakeShape and Gatsby](../Images/078b2137b04532b0d8fd2f94998033b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U4woHalC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mezesbggcbufgpidb0cg.gif)

这是我们 4 部分系列的第 4 部分。查看[第 1 部分](https://www.takeshape.io/articles/how-to-build-a-design-portfolio-with-takeshape/)，了解 TakeShape 的介绍，并学习内容建模和内容创建。在第 2 部分的[中，您将学习如何创建一个静态站点，从](https://www.takeshape.io/articles/how-to-build-a-jamstack-website-with-takeshape/) [TakeShape](https://www.takeshape.io/) 中加载数据。在第 3 部分中，您将学习如何快速地将新的静态站点部署到 Netlify 上，以获得一个简单、安全、快速的托管解决方案。

**TLDR:** 缩短本教程，开始摆弄已完成的网站。我们不会被冒犯的！[登录](https://app.takeshape.io/login)，用“形状组合”模板创建一个新项目。然后克隆完成的项目 repo，检出`README.md`开始卡壳。

```
> git clone https://github.com/takeshape/takeshape-samples
> cd takeshape-samples/shape-portfolio-gatsbyjs 
```

## 大胆和冒险应该继续

您已经到达了这个系列的最后一部分，它向您展示了如何用 [TakeShape](https://www.takeshape.io/) 创建一个静态站点组合。如果你从一开始就跟着做，你会取得很大的成就！您已经使用 TakeShape 创建了一个内容模型和 GraphQL API，使用 TakeShape 的静态站点生成器将您的内容加载到一个完全可定制的静态站点中，并使用 Netlify 部署了您的静态站点。

在这个结论中，您将通过学习如何使用 [Gatsby.js](https://www.gatsbyjs.org/) 来构建您的作品集网站，这是一个静态网站生成器，它将 React.js 和 Webpack 等现代 web 工具置于 JAMstack 网站快速安全的基础之上。Gatsby 是另一个很棒的 JAMstack 工具，可以用 GraphQL APIs 中的数据生成一个 JAMstack 站点，特别是对于高度交互的 web 应用程序。TakeShape 为你所有的 Gatsby 站点提供了一个很好的 CMS 后端，因为每个 TakeShape 项目都有一个 GraphQL API，可以很容易地与 Gatsby 集成。

在本演练中，您将从 Gatsby 开始，并将现有的 portfolio 网站转换为基于 React 的网站。这是本系列中最高级的演练，所以如果您还没有通读前面的文章，我们建议您至少先浏览一下。

## 你好盖茨比！

从克隆 Gatsby starter 项目“hello world”开始，并确保一切正常。安装所需的库，然后用`npm start`运行开发人员站点。

```
> git clone https://github.com/gatsbyjs/gatsby-starter-hello-world shape-portfolio-gatsbyjs
> cd shape-portfolio-gatsbyjs
> npm install
> npm start 
```

在浏览器中访问`localhost:8000`,查看入门网站的运行情况！

## 将盖茨比连接成一个形状

由于它们都是围绕 GraphQL 数据构建的，Gatsby 和 TakeShape 就像乐高积木一样合二为一。在将 TakeShape 作为 Gatsby 的数据源之后，您将能够直接从页面和组件中查询数据。

首先，安装`gatsby-source-graphql`和`dotenv`插件:

```
> npm install gatsby-source-graphql dotenv 
```

然后在项目的根目录下创建一个`gatsby-config.js`文件来配置插件:

```
require("dotenv").config();

module.exports = {
    plugins: [
        {
            resolve: "gatsby-source-graphql",
            options: {
                typeName: "TS",
                fieldName: "takeshape",
                // Url to query from
                url: `https://api.takeshape.io/project/${process.env.TAKESHAPE_PROJECT}/graphql`,
                // HTTP headers
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${process.env.TAKESHAPE_TOKEN}`,
                },
                // Additional options to pass to node-fetch
                fetchOptions: {},
            },
        },
    ]
}; 
```

您会注意到这个配置使用了本地环境中的`TAKESHAPE_PROJECT`和`TAKESHAPE_TOKEN`值。我们将把这些值保存在我们的环境中，这样我们就不会无意中把这些秘密提交给版本控制。

Gatsby starter 项目应该已经包含了一个包含了`.env`的`.gitignore`文件，但是您可能想要仔细检查一下，以防万一。

然后，[创建一个只读的 TakeShape API 令牌](https://www.takeshape.io/docs/creating-an-api-key/)，并从 TakeShape web 客户端的 URL 中复制项目的 ID。最后，在项目的根目录下创建一个名为`.env`的文件，如下所示:

```
TAKESHAPE_PROJECT=<YOUR-PROJECTS-ID>
TAKESHAPE_TOKEN=<YOUR-API-TOKEN> 
```

### 测试您的连接

用`gatsby develop`启动你的 Gatsby 开发服务器。只要配置正确，您将看到服务器成功启动，并且能够使用内置的 GraphiQL IDE 测试您的连接。

服务器启动后，在浏览器中导航至`http://localhost:8000/___graphql`。您将看到一个类似于 TakeShape 中的 API Explorer 的页面——因为它是相同的软件！

使用这个 GraphQL 查询:
测试您是否可以通过 Gatsby 访问您的投资组合项目

```
query  {  takeshape  {  getProjectList  {  total  }  }  } 
```

当您键入时，您应该看到不同的字段自动完成。然后，当您运行查询时，您应该会看到到目前为止您已经创建的投资组合项目的总数。正如您所看到的，Gatsby 可以很容易地与 TakeShape 对话，为您的整个项目找出模式，并让您将这些数据直接引入您的 Gatsby 项目。

现在，我们可以开始在 React 中重新创建我们的投资组合了！

## 从双节棍迁徙中反应过来

我们将从为您的投资组合中的每个项目创建页面开始，然后转移到重新创建主页和关于页面的更简单的任务。

但是在我们做任何事情之前，我们需要将基本布局和结构迁移到 React！

### 布局

首先，我们将在项目的`src`目录中创建一个名为`layouts`的文件夹。然后我们将创建一个名为`default.js`的文件。您可以从项目的根目录用两个命令做到这一点:

```
> mkdir src/layouts && touch src/layouts/default.js 
```

我们的`default.js`布局在目的上与你在第 2 部分中为`shape-portfolio`创建的 Nunjucks 模板中的`default.html`布局非常相似，尽管因为它是用 React 构建的，所以看起来会略有不同:

```
import React from "react";
import PropTypes from "prop-types";

import Header from "../components/header";
import Footer from "../components/footer";
import Menu from "../components/menu";
import "./default.css";

const Layout = ({children}) => (
    <>
        <Header siteTitle="Shape Portfolio"/>
        <div className="container">
            <Menu/>
            <div className="main">{children}</div>
            <Footer/>
        </div>
    </> );

Layout.propTypes = {
    children: PropTypes.node.isRequired
};

export default Layout; 
```

该组件的目的是提供一个基本布局，所有页面都可以从该布局继承。通过用这种布局包装其他组件，可以确保项目中的所有页面保持一致的外观，并且不会重复任何功能。您将使`children`成为布局组件的一个必需属性，因为它应该总是充当其他东西的包装器，无论是项目列表、项目本身还是其他完全不同的东西。

接下来在同一个`src/layouts`目录中创建一个`default.css`文件。你会注意到它也被导入到这个`default.js`文件中。现在你可能会说，“把 CSS 导入 JS？那是行不通的！”因为 Gatsby 内置了 Webpack，所以当需要建立你的网站时，它会提取并打包你所有的 CSS。这样做的好处是可以让你的风格和你的组件保持一致。

现在，继续将`shape-portfolio`项目中的`main.css`样式复制到`default.css`中。在本演练中，您不会深入高级样式，尽管 Gatsby 通过附加插件支持的[更高级的 CSS-in-JS 技术值得更深入地探索。](https://www.gatsbyjs.org/docs/styling/)

#### 页眉&页脚

在您的`default.js`布局中，页眉、页脚和菜单都被抽象成独立的组件。您将从创建头文件开始，它将为您的项目提供所有的`<head>`标签。

首先将`react-helmet`和`gatsby-plugin-react-helmet`包添加到您的项目:

```
> npm install react-helmet gatsby-plugin-react-helmet 
```

然后将`gatsby-plugin-react-helmet`添加到`gatsby-config.js`文件中的插件列表:

```
module.exports = {
    plugins: [
    'gatsby-plugin-react-helmet',
     {
       resolve: "gatsby-source-graphql",
       …
      }
  ]
} 
```

[React 头盔](https://github.com/nfl/react-helmet)是一个易于使用的组件，用于管理所有页面的文档头。它会将您提供的`<head>`标签附加到您包含它的每个页面的开头——因为您将它包含在您的默认布局中，所以它会包含在每个页面中。

在您的`src`目录中，创建一个新的`components`目录，并在其中创建一个名为`header.js`的新文件，如下所示:

```
import React from "react";
import Helmet from "react-helmet";

const Header = ({siteTitle}) => (
    <Helmet>
        <html lang="en"/>
        {siteTitle}
        <meta charSet="utf-8"/>
        <meta name="viewport" content="width=device-width, initial-scale=1"/>
        <link
            href="https://fonts.googleapis.com/css?family=Work+Sans:400,500,600,700"
            rel="stylesheet"
        />
    </Helmet> );

export default Header; 
```

这就是创建一个 Header 组件的全部工作，它将被导入并由您的默认布局使用。

接下来在`src/components/footer.js`中创建你网站的页脚。这个组件比较简单:

```
import React from "react";

const Footer = () => (
    <footer className="footer">
        <p>
            © {new Date().getFullYear()}{"  "}
            <a
                href="https://takeshape.io"
                target="_blank"
                rel="noopener noreferrer nofollow"
            >
                TakeShape Inc.
            </a>
        </p>
    </footer> );

export default Footer; 
```

您会注意到这个页脚包含了我们的第一个动态内容:通过使用`new Date().getFullYear()`函数，版权日期将自动反映当前年份。在组件内部使用 JavaScript 的能力是 React 最强大的方面之一！

#### 菜单

您将创建的最终布局组件也将是第一个从 TakeShape 项目中查询数据的组件。为了启用这个功能，您将使用 Gatsby 提供的 [`StaticQuery` API](https://www.gatsbyjs.org/docs/static-query/#static-query) 。使用`StaticQuery`，一个组件可以单独请求它需要呈现的数据。这非常适合您的菜单组件，因为无论页面包含什么其他内容，它在每个页面上都呈现相同的内容。

为了从 TakeShape 呈现图像，您还将使用 TakeShape 的`takeshape-routing`包提供的`getImageUrl` API。

首先，安装`takeshape-routing`包:

```
> npm install takeshape-routing 
```

然后，创建一个类似于
的`src/components/menu.js`组件

```
import React from "react";
import { Link, StaticQuery, graphql } from "gatsby";
import { getImageUrl } from "takeshape-routing";

import routes from "../routes";

const menuQuery = graphql`
  query {
    takeshape {
      projects: getProjectList {
        items {
          name
        }
      }
      about: getAbout {
        socialProfiles {
          profileUrl
          socialNetwork
          socialNetworkIcon {
            path
          }
        }
      }
    }
  }
`;

const Menu = ({ data }) => (
  <nav className="menu">
    <div className="menu__container">
      <p className="site-name">
        <Link to="/">
          <strong>Shape Portfolio</strong>
        </Link>
      </p>
      <ul className="nostyle">
        {data.takeshape.projects.items.map((project, i) => (
          <li key={i}>
            <Link to={routes.project(project.name)}>{project.name}</Link>
          </li>
        ))}
      </ul>
      <hr />
      <Link to="/about">About</Link>
      <ul className="nostyle inline">
        {data.takeshape.about.socialProfiles.map((profile, i) => (
          <li className="social-profile" key={i}>
            <a
              href={profile.profileUrl}
              target="_blank"
              rel="noopener noreferrer"
            >
              <img
                className="social-network-icon"
                src={getImageUrl(profile.socialNetworkIcon.path)}
                alt="Social network icon"
              />
              <span className="social-network-name">
                {profile.socialNetwork}
              </span>
            </a>
          </li>
        ))}
      </ul>
    </div>
  </nav> );

export default () => (
  <StaticQuery query={menuQuery} render={data => <Menu data={data} />} />
); 
```

该菜单由两个对象组成:对我们想要包含的数据的查询和呈现它的组件。在文件的底部，这两个对象被传递给 Gatsby 的`StaticQuery` API，它承担获取数据和呈现组件的工作。

菜单也利用了盖茨比的`Link`成分。注意你如何使用`Link`在你的站点内链接，就像你链接到一个项目页面，而你如何使用一个标准锚链接到你的站点外，就像你链接到一个社交网络。当你使用`Link`时，Gatsby 可以用 Javascript 动态加载这些新页面，防止额外的页面加载。这是一个灵活的开箱即用的功能。

最后，`Link`使用函数`routes.project`设置它的目的地。您需要自己创建这个函数。幸运的是，这是下一步！

### 项目页面

您需要为每个项目创建一个独立的页面，这样您就可以直接链接到您的投资组合中的不同项目。但是，每个页面也应该是动态创建的，基于从 TakeShape 的数据。那么，如何动态地创建页面呢？

首先，在站点的根目录下创建一个名为`gatsby-node.js`的新文件。这将包含由 Gatsby 的构建过程执行的代码。您将插入 Gatsby 的`createPages` API，为您投资组合的每个项目创建一个独立的页面。

首先创建文件:

```
> touch gatsby-node.js 
```

然后添加您的功能:

```
const path = require("path");
const routes = require("./src/routes");

exports.createPages = async ({ actions, graphql }) => {
  const { data } = await graphql(`
    query {
      takeshape {
        projects: getProjectList {
          items {
            _id
            name
          }
        }
      }
    }
  `);

  data.takeshape.projects.items.forEach(({ _id, name }) => {
    actions.createPage({
      path: routes.project(name),
      component: path.resolve("./src/components/Project.js"),
      context: {
        projectId: _id
      }
    });
  });
}; 
```

该函数将首先查询 TakeShape 以获得所有项目名称和 id 的列表。然后，对于每个项目，您将使用`actions.createPage` API 来创建它的独立页面。每页需要三样东西:

1.  一个路径，您将使用路由函数使用项目名称来生成该路径。
2.  您将创建一个组件来呈现项目页面。
3.  您将为每个项目页面的单独 GraphQL 查询提供一个上下文，

#### 创建您的项目路径

您将创建为项目生成路径的函数。首先在`src`目录下创建一个名为`routes.js`的文件，并将`slugify`包添加到您的项目中。

```
> touch src/routes.js  
> npm install slugify 
```

接下来，在`src/routes.js`中，添加一个基于项目名称为项目创建路径的函数:

```
const slugify = require("slugify");

exports.project = function(name) {
  const slug = slugify(name.toLowerCase());
  return `/projects/${slug}/`;
}; 
```

当您将其他动态生成的页面添加到您的文件夹中时，您可以将它们的路由规则添加到同一文件中。

#### 创建您的项目组件并查询

您的组件将呈现整个项目，您将把它包装在您已经创建的`Layout`组件中。这将为每个项目呈现一个完整的页面！

首先在您的`src/components`目录中创建一个名为`Project.js`的文件，看起来像这样:

```
import React from "react";
import { graphql } from "gatsby";
import { getImageUrl } from "takeshape-routing";

import Layout from "../layouts/default";

export const ProjectMetadata = ({ startDate, endDate, client }) => {
  const startYear = new Date(startDate).getFullYear();
  const endYear = endDate ? new Date(endDate).getFullYear() : null;
  const includeEndYear = endYear && startYear !== endYear;
  return (
    <div className="project__metadata">
      <p className="project__metadata">
        {startYear}
        {includeEndYear ? `&endash; ${endYear}` : ""}
      </p>
      {client ? (
        <p>
          <a href={client.url} target="_blank" rel="noopener noreferrer">
            {client.name}
          </a>
        </p>
      ) : (
        ""
      )}
    </div>
  );
};

export const Project = ({ project }) => (
  <article className="project">
    <header>
      {project.coverImage ? (
        <img
          className="project__cover-image"
          alt={project.coverImage.description}
          src={getImageUrl(project.coverImage.path, {
            h: 600,
            w: 1200,
            fit: "crop"
          })}
        />
      ) : (
        ""
      )}
      <h1>{project.name}</h1>
      <ProjectMetadata
        startDate={project.startDate}
        endDate={project.endDate}
        client={project.client}
      />
    </header>
    <div
      className="project__description"
      dangerouslySetInnerHTML={{ __html: project.description }}
    />
  </article> );

export default ({ data }) => (
  <Layout>
    <Project project={data.takeshape.project} />
  </Layout> );

export const query = graphql`
  query($projectId: ID!) {
    takeshape {
      project: getProject(_id: $projectId) {
        name
        startDate
        endDate
        coverImage {
          description
          path
        }
        client {
          name
          url
        }
        description: descriptionHtml
      }
    }
  }
`; 
```

从底部开始，这个文件导出一个针对单个项目和组件的查询，该查询呈现完整的项目页面，这两者都由`createPage` API 使用。该查询接受您提供给`createPage` API 的`context`，并获取每个项目页面的数据。然后它用`data`参数将其提供给页面。页面将项目组件包装在布局组件中，以便呈现完整的页面。

该文件中的`Project`组件提供了实际呈现每个项目的逻辑。它还使用一个`ProjectMetadata`子组件来处理格式化项目元数据的逻辑。

### 首页

您的主页将提供您的项目列表，我们将在一个单独的可重用组件中创建该列表。主页将为项目列表提供自己的查询，然后将其传递给`ProjectList`。

您使用的 Gatsby starter 项目已经在`src/pages`目录中包含了一个名为`index.html`的文件。`src/pages`是 Gatsby 中的一个特殊目录:这个目录中的每个文件都会自动创建自己的独立页面，只要文件至少导出一个组件。

把你的`src/pages/index.html`更新成这样:

```
import React from "react";
import { graphql } from "gatsby";

import Layout from "../layouts/default";
import ProjectList from "../components/ProjectList";

const IndexPage = ({ data }) => (
  <Layout>
    <ProjectList projects={data.takeshape.projects} className="main" />
  </Layout> );

export default IndexPage;

export const query = graphql`
  query {
    takeshape {
      projects: getProjectList {
        items {
          name
          startDate
          coverImage {
            description
            path
          }
        }
      }
    }
  }
`; 
```

然后，在您的`src/components`目录中创建一个名为`ProjectList.js`的文件，如下所示:

```
import React from "react";
import { Link } from "gatsby";
import { getImageUrl } from "takeshape-routing";

import routes from "../routes";

const ProjectListItem = ({ project }) => {
  const startYear = new Date(project.startDate).getFullYear();
  const endYear = project.endDate
    ? new Date(project.endDate).getFullYear()
    : null;
  const includeEndYear = endYear && startYear !== endYear;
  return (
    <Link to={routes.project(project.name)} className="project">
      <img
        className="project__thumbnail"
        src={getImageUrl(project.coverImage.path, {
          h: 200,
          w: 300,
          fit: "crop"
        })}
        alt={project.description}
      />
      <p className="project__name">
        <strong>{project.name}</strong>
      </p>
      <p class="project__metadata">
        {startYear}
        {includeEndYear ? `&endash; ${endYear}` : ""}
      </p>
    </Link>
  );
};

const ProjectList = ({ projects }) => (
  <ul className="nostyle project-list">
    {projects.items.map((project, i) => (
      <li className="project-list--entry" key={i}>
        <ProjectListItem project={project} />
      </li>
    ))}
  </ul> );

export default ProjectList; 
```

在这个文件中，`ProjectList`组件呈现了一组`ProjectListItem`组件。每个`ProjectListItem`显示一些关于项目的有限信息和你已经创建的项目页面的链接。这个组件不需要包含查询，因为它不是一个独立的页面，数据是由包含它的`index.js`文件提供的。

此时，您应该能够运行`gatsby develop`，导航到`http://localhost:8000/`，并看到项目列表。点击其中的任何一个都会带你到那个项目的页面。它还活着！

### 约及 404 页

最后，您将创建一个独立的 About 页面和一个独立的 404 页面。对于 about 页面，您可以在`src/pages`中创建一个类似于
的文件`about.js`

```
import React from "react";
import { graphql } from "gatsby";
import { getImageUrl } from "takeshape-routing";

import Layout from "../layouts/default";

const AboutPage = ({ data }) => (
  <Layout>
    <article className="about">
      <img
        className="about__portrait"
        src={getImageUrl(data.takeshape.about.portrait.path, {
          h: 150,
          w: 150,
          fit: "crop"
        })}
      />
      <div
        className="about__biography"
        dangerouslySetInnerHTML={{ __html: data.takeshape.about.biography }}
      />
    </article>
  </Layout> );

export default AboutPage;

export const query = graphql`
  query {
    takeshape {
      about: getAbout {
        biography: biographyHtml
        portrait {
          title
          description
          path
        }
      }
    }
  }
`; 
```

该文件应该导出您的`AboutPage`组件和一个查询，以便页面可以自己呈现。

对于 404 页面，您只需用您的`Layout`组件包装几行，然后就到此为止:

```
import React from "react";
import Layout from "../layouts/default";

const NotFoundPage = () => (
  <Layout>
    <h1>NOT FOUND</h1>
    <p>You just hit a route that doesn&#39;t exist... the sadness.</p>
  </Layout> );

export default NotFoundPage; 
```

## 预览您的生产现场

运行`gatsby develop`可以让你运行一个开发友好的站点版本，当你做出改变时，有一些简洁的特性，比如组件的热重载。

要预览您的站点在生产中的样子，您可以使用`gatsby build`和`gatsby serve`命令:

*   运行`gatsby build`后，你的网站将被编译并保存到项目顶部一个名为`public`的文件夹中。
*   运行`gatsby serve`后，您可以在浏览器中的`localhost:9000`访问您的生产预览。

## 将您的新网站部署到网络生活中

现在你的项目完成了，是时候上线了！通过使用 Gatsby，您将绕过 TakeShape 的内置静态站点生成器，因此您将需要使用 Netlify 的持续集成功能和 TakeShape 的 webhooks 功能的组合来手动设置您的部署系统。

首先确保你的项目托管在一个远程的 Git 仓库中，比如 GitHub。然后从[网站页面](https://app.netlify.com/account/sites)的 Git 创建一个**新网站。授权 Netlify 访问项目的存储库，然后使用默认的构建配置。Netlify 应该自动识别出您正在使用 Gatsby 作为静态站点生成器，并正确配置您的构建设置。然后点击**部署站点**。**

第一次部署应该会失败。没关系！您还没有在 TakeShape 中将您的`TAKESHAPE_PROJECT`和`TAKESHAPE_TOKEN`设置添加到您的环境中，所以您应该预料到它会失败。

要解决这个问题，请转到您的 Netlify 站点的设置，然后转到“构建和部署”部分。从那里，您可以编辑您的“构建环境变量”来安全地添加您的 TakeShape 凭证。点击**编辑变量**，然后为`TAKESHAPE_PROJECT`和`TAKESHAPE_TOKEN`添加变量。点击**保存**。

然后，转到您站点的部署部分，单击**触发部署**。您将看到您的部署脚本运行，并在运行过程中记录消息。最后，您应该会看到成功消息，并最终声明您的“站点已上线”！恭喜你。！！🎉如果你回到你的网站的概述，你应该看到一个链接到你的新网站。

最后，您需要设置一个构建 webhook *(所有付费 TakeShape 计划都提供 web hook)*，这样当您在 TakeShape 中创建、更新或删除内容时，您可以告诉 Netlify 重建您的网站。回到你的 Netlify 站点的“Build & Deploy”设置中，寻找标有“Build hooks”的部分。点击**添加构建钩子**，给它一个类似“TakeShape”的名字，点击**保存**，然后复制为你创建的 URL。

然后，在 TakeShape 中，在**项目设置> Webhooks** 中导航到您项目的 webhook 设置。配置一个新的 webhook，这样你刚才复制的 URL 就是 **Webhook URL** ，触发它的**资源**就是`Content: *`。并确保检查创建、更新和删除操作。将**机密**字段留空，并点击**保存**。

现在，每当您创建一个新的内容对象、更新一个现有的内容对象或删除一个内容对象时，它都会触发 Netlify 中您的站点的新构建。这样，您的站点将始终与您的 TakeShape 项目保持同步。

## 结论

如果你已经走到这一步，恭喜你！你一头扎进 JAMstack 活了下来。希望您现在对可配置 CMS、自动生成的 GraphQL API 和强大的 React-powered 静态站点的强大功能有了深刻的理解。JAMstack 是 web 开发的未来，你现在处于最前沿。

如果您对我们的系列有任何反馈、意见或问题，请随时联系我们。我们随时欢迎您通过我们的应用内实时聊天或通过 [support@takeshape.io](//mailto:support@takeshape.io) 提供反馈。

## 对 Gatsby.js 的无头 CMS 感兴趣？

TakeShape 是一个无头的 GraphQL CMS，它使得用 Gatsby 构建 JAMstack 更加容易。在 TakeShape，我们正在为最具创造力的设计师和开发人员打造最好的内容管理工具。我们的项目模板让您轻松入门。此外，价格完全可以承受。[注册一个免费账户](https://app.takeshape.io/signup)，花更多的时间发挥创造力！