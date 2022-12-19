# 使用 React-Docgen ⚛⚙️自动化 React 文档📚

> 原文：<https://dev.to/whoisryosuke/automating-react-documentation-using-react-docgen--3mgh>

为了让文档尽可能的简单无缝，我一直在探索，我开始尝试一个由脸书团队开发的名为 [**react-docgen**](https://github.com/reactjs/react-docgen) 的库。

它是一个 CLI 和 API，可以读取你的 React 组件文件，抓取任何留在注释中的文档，并抽取出一个包含所有文档和道具的对象:

```
{
  "props": {
    "foo": {
      "type": {
        "name": "number"
      },
      "required": false,
      "description": "Description of prop \"foo\".",
      "defaultValue": {
        "value": "42",
        "computed": false
      }
    },
    "bar": {
      "type": {
        "name": "custom"
      },
      "required": false,
      "description": "Description of prop \"bar\" (a custom validation function).",
      "defaultValue": {
        "value": "21",
        "computed": false
      }
    }
  },
  "description": "General component description."
} 
```

Enter fullscreen mode Exit fullscreen mode

一些设计系统和框架使用 *react-docgen* 作为他们生成文档的过程的一部分。通常，您将 CLI 输出的对象保存到 JSON 文件中，以便在其他脚本/工具中使用。非常适合使用诸如 GatsbyJS、Jekyll 或 Slate 之类的 SSG(静态站点生成器)来生成内容/降价文件。

我测试了使用 react-docgen 记录 react 代码的两种方法。我的目标是使用 GatsbyJS 作为我的静态站点生成器，并基于 CLI 数据构建一个文档站点。

> 如果您对更“开箱即用”的解决方案感兴趣，请尝试 [react-styleguidist](https://github.com/styleguidist/react-styleguidist) 。它使用 react-docgen 创建所有组件的一页文档。

让我们开始吧，⚡️

* * *

## 流程

我在上面描述了基本过程:我们希望运行一个使用 react-docgen API 的脚本，并生成一个 react 文档的 JSON 文件，我们可以在站点的构建过程中使用它。

让我们先试一下👇

## 工具脚本+盖茨比输出

这个过程非常简单:

1.  创建获取所有组件的脚本，并使用 react-docgen API 将文件解析为 JSON(保存为文件输出- `components.json`)
2.  GatsbyJS `gatsby-node.js`脚本解析 JSON 并创建页面。

首先我们制作构建脚本:

```
const fs = require("fs");
const path = require("path");
const reactDocs = require("react-docgen");

// The React components to load
const componentFolder = "./src/components/";

// Where the JSON file ends up
const componentJsonPath = "./docs/components.json";

const componentDataArray = [];

function pushComponent(component) {
  componentDataArray.push(component);
}

function createComponentFile() {
  const componentJsonArray = JSON.stringify(componentDataArray, null, 2);
  fs.writeFile(componentJsonPath, componentJsonArray, "utf8", (err, data) => {
    if (err) {
      throw err;
    }
    console.log("Created component file");
  });
}

/**
 * Use React-Docgen to parse the loaded component
 * into JS object of props, comments
 *
 * @param {File} component
 * @param {String} filename
 */
function parseComponent(component, filename) {
  const componentInfo = reactDocs.parse(component);
  const splitIndex = filename.indexOf("/src/");
  const shortname = filename.substring(splitIndex + 4);

  componentInfo.filename = shortname;

  pushComponent(componentInfo);
}

/**
 * Loads a component file, then runs parsing callback
 * @param {String} file
 * @param {Promise} resolve
 */
function loadComponent(file, resolve) {
  fs.readFile(file, (err, data) => {
    if (err) {
      throw err;
    }

    // Parse the component into JS object
    resolve(parseComponent(data, file));
  });
}

/**
 * Explores recursively a directory and returns all the filepaths and folderpaths in the callback.
 *
 * @see http://stackoverflow.com/a/5827895/4241030
 * @param {String} dir
 * @param {Function} done
 */
function filewalker(dir, done) {
  let results = [];

  fs.readdir(dir, async (err, list) => {
    if (err) return done(err);

    let pending = list.length;

    if (!pending) return done(null, results);

    list.forEach(file => {
      file = path.resolve(dir, file);

      fs.stat(file, async (err, stat) => {
        // If directory, execute a recursive call
        if (stat && stat.isDirectory()) {
          filewalker(file, (err, res) => {
            results = results.concat(res);
            if (!--pending) done(null, results);
          });
        } else {
          // Check if is a Javascript file
          // And not a story or test
          if (
            file.endsWith(".js") &&
            !file.endsWith(".story.js") &&
            !file.endsWith(".test.js")
          ) {
            await new Promise(resolve => {
              loadComponent(file, resolve);
            });
            await results.push(file);
          }
          if (!--pending) done(null, results);
        }
      });
    });
  });
}

filewalker(componentFolder, (err, data) => {
  if (err) {
    throw err;
  }

  createComponentFile();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用我在 Github 上找到的一个名为`firewalker()`的函数，它加载一个文件夹并“遍历”每个文件。当我们遍历每个文件时，我们检查它是否是一个 JS 文件(而不是一个测试或故事书 JS 文件)，然后运行`loadComponent()`函数，它是 Node 用于加载文件的 API 的包装器。

一旦组件文件被实际加载，我们运行`parseComponent()`函数，该函数实际上在我们的文件上运行 **react-docgen** 。最后，我们将生成的文档数据“推”到一个数组中。在所有文件加载之后，我们的`firewalker()`函数有一个回调函数运行一个`createComponentFile()`函数，该函数输出实际的 JSON 文件。

### 使用脚本

现在，我们可以通过在 Node 的 CLI 中运行脚本来生成一个包含所有组件的 JSON 文件:

`node generate-documentation.js`

GatsbyJS 支持使用 JSON 文件作为“源”来构建站点，从使用 [gatsby-transformer-json](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-json) 。当我们加载生成的文件时，它将 JSON 转换成我们可以查询的 GraphQL 节点。

## 纯粹的盖茨比

最后一种方法是可行的，但是它看起来很麻烦并且容易出错，必须依赖一个单独的工具脚本。经过一点研究，我发现了第二种更完整的处理方法。

1.  使用 Gatsby 的 react-docgen 插件，该插件从某个文件夹中抓取所有组件，生成 JSON，并为其旋转一个 GraphQL 端点。

我们安装插件并将其添加到我们的 Gatsby 配置中(以及导入我们的组件的文件系统源):

**gatsby-config.js** :

```
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `components`,
        // Location of your React components
        path: `../src/components/`,
      },
    },
    // "Transforms" our "source" of React/JS files with the react-docgen CLI
    // and creates a GraphQL node with the output
    `gatsby-transformer-react-docgen`,
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

然后显示我们的数据就像查询 GraphQL:
一样简单

```
import React, { Component } from 'react'
import { graphql } from 'gatsby'
import Layout from '../components/layout'

import PropsTable from '../components/propstable'

export default class ComponentPage extends Component {
  render() {
    const { children, data, tableOfContents } = this.props
    console.log('mdx', data.mdx)
    console.log('component metadata', data.componentMetadata)
    return (
      <Layout>
        <div className="content">
          {children}
          <h1>{data.componentMetadata.displayName}</h1>
          <p>{data.componentMetadata.docblock}</p>
          <h2 style={{ marginTop: '2rem' }}>Props:</h2>
          <PropsTable
            propMetaData={data.componentMetadata.childrenComponentProp}
          />
        </div>
      </Layout>
    )
  }
}

export const pageQuery = graphql`
  query($name: String!) {
    componentMetadata(displayName: { eq: $name }) {
      id
      displayName
      docblock
      doclets
      childrenComponentProp {
        name
        docblock
        required
        parentType {
          name
        }
        type {
          value
        }
        defaultValue {
          value
          computed
        }
      }
      composes
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

### 奖励:道具表

为了显示我们的道具的所有数据(描述，默认值，它是必需的吗？等等)，我们创建一个组件，它从 react-docgen CLI 接受我们的 props 并输出一个表。[我在 Github](https://github.com/episodeyang/react-component-props-table/) 上找到了这个，并用 Gatsby 版本的 react-docgen:
修改了它

```
import React, { Component } from 'react'
import PropTypes from 'prop-types'

/**
 * Table for React props generated by react-docgen
 *
 * @see https://github.com/episodeyang/react-component-props-table/
 */
const PropsTable = props => {
  let { className = '', propMetaData = [], ..._props } = props
  if (className) className += ' component-props-table'
  return (
    <table className={className} {..._props}>
      <thead>
        <tr>
          <th>Prop Name</th>
          <th>Type</th>
          <th>Is Required</th>
          <th>Default Value</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        {Object.keys(propMetaData).map(key => {
          const prop = propMetaData[key]
          return (
            <tr key={key}>
              <td style={{ color: 'rgb(17, 147, 154)' }}>{prop.name}</td>
              <td>{prop.parentType ? prop.parentType.name : ''}</td>
              {prop.required ? (
                <td style={{ color: 'rgb(255, 76, 34)' }}>required</td>
              ) : (
                <td style={{ color: '#c6c6c6' }}>optional</td>
              )}
              {prop.defaultValue ? (
                <td style={{ color: 'rgb(236, 171, 32)' }}>
                  {prop.defaultValue.value}
                </td>
              ) : (
                <td style={{ color: '#c6c6c6' }}>none</td>
              )}
              {prop.docblock ? <td>{prop.docblock}</td> : <td />}
            </tr>
          )
        })}
      </tbody>
    </table>
  )
}

PropsTable.propTypes = {
  /** this is the `metadata.props` field of what metadata you get from the react-docgen-loader.  */
  propMetaData: PropTypes.object,
}
PropsTable.defaultProps = {
  propMetaData: {},
}

export default PropsTable 
```

Enter fullscreen mode Exit fullscreen mode

更高效，因为它在构建时运行 react-docgen，而不是要求我们单独运行脚本(或者将其与我们的构建过程挂钩)。

还将文档作为 GraphQL 端点导入，允许我们查询数据——而不是硬导入一个*(可能很大)* JSON 文件——或者使用`gatsby-transformer-json`插件(它不为 GraphQL 以及特定的 react-docgen 插件格式化数据)。

[![Gatsby Documentation Starter using gatsby-transformer-react-docgen](../Images/16de84de42426651088af4ffac796831.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ipsXLqX7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/screenshot-e991536017bd42e7c1e36696cc0ff3cc-e2462.png)

你可以在 Github 上下载最终的“纯盖茨比”版本:[盖茨比文档启动器](https://github.com/whoisryosuke/gatsby-documentation-starter)。

## 将文档记录下来

我希望这有助于你理解文档背后的过程，或者特别是 React 组件文档。使用 react-docgen CLI 或 API 使过程变得简单，只需加载组件文件并通过库提供它们，抽取出适合前端接口的结构化数据。

有很多现成的解决方案，但是理解它们是如何工作的总是有益的(揭开*魔法* ✨的神秘面纱)，特别是如果你想创造一些新的和新鲜的东西*(框架只能到此为止)*。

Cheers,
Ryo

* * *

**参考文献**:

*   [react-docgen](https://github.com/reactjs/react-docgen)
*   [react-docgen 官方举例](https://github.com/reactjs/react-docgen/blob/master/example/buildDocs.sh)
*   [查看 react-styleguidist 如何使用 react-docgen](https://github.com/styleguidist/react-styleguidist/search?q=docgen&unscoped_q=docgen)
*   [盖茨比-变压器-反应-文件生成](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-react-docgen)
*   [React 组件道具表](https://github.com/episodeyang/react-component-props-table/)
*   [盖茨比文档启动器](https://github.com/whoisryosuke/gatsby-documentation-starter)