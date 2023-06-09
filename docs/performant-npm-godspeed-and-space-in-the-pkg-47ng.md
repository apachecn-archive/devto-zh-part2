# 表演 NPM:PKG 的天佑平安和太空

> 原文：<https://dev.to/vblaha/performant-npm-godspeed-and-space-in-the-pkg-47ng>

[![img](img/6c2213eb4feafc9c399a034f04f40138.png)](https://camo.githubusercontent.com/6b77769ede95c3dd4b6b331b707ef7afb82a4de0/68747470733a2f2f6f70656e636f6c6c6563746976652e636f6d2f706e706d2f636f6e7472696275746f72732e7376673f77696474683d38393026627574746f6e3d66616c7365) pnpm 构建在 Node.js 平台上，其真正的强大之处在于它能够挤压磁盘空间，并利用 npm 的配置能力作为命令的路径。虽然我还没有机会使用 pnpm 迁移文件，但我越来越好奇如何使用经典的 Unix 符号链接和硬链接结构来修改特殊项目的节点模块。Pnpm 利用这些链接结构来防止节点模块中的默认行为产生的错误。举例说明 pnpm 的严格性，[请阅读](https://medium.com/pnpm/pnpms-strictness-helps-to-avoid-silly-bugs-9a15fb306308)。

# 符号链接

在这个上下文中，*符号链接*的简单定义是一个文件，它是另一个文件的快捷方式。pnpm 通过 hardlink 从全局存储中提取数据，创建一个版本的节点模块，并在磁盘上只存储一次。符号链接充当 express、npm 注册表和模块文件之间的软连接。这使得 pnpm 能够将依赖项移动到依赖包的实际位置所在的同一目录级别。如果你对这个文件结构很好奇，你可以在 pnpm 中阅读更多关于[模块结构的内容。](https://dev.to/zkochan/flat-nodemodules-is-not-the-only-way-mo2)

# 对等依赖

对等依赖关系是横向安装的，在依赖关系顺序中处于较高的位置。如果不存在对等项，则使用符号链接将程序包硬链接到 node_modules 以嵌套依赖项，这有助于防止再循环。将为每个对等体安装对等体依赖关系，Node.js 将使用[模块解析器算法](https://nodejs.org/api/modules.html#modules_all_together)来找到正确的对等体。

# 过滤

pnpm 还通过一系列递归样式命令在多个依赖包中利用过滤。

`pnpm recursive install`
为每个子文件夹中的每个包运行安装
`pnpm recursive run build --filter foo-*`
构建名称以 foo-
`pnpm recursive update -- login-page...`
开头的所有包更新登录页面中的依赖项以及存储库中登录页面的任何依赖项

# 挂钩

pnpm 允许您通过称为钩子的特殊函数直接进入安装过程。钩子可以在名为 pnpmfile.js 的文件中声明，pnpmfile.js 应该位于项目的根目录中。

pnpmfile.js 更改依赖项的依赖项字段的例子:
[![screenshot](img/3db50dd7a9f5845e685d9729facbefc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzPELiDz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/es24k1fciv2d2yyq3uxa.png)

这个钩子将在从注册表下载 foo@1 的清单后覆盖它

# 别名

别名允许您安装和发布具有特殊名称的包，允许您在项目中使用不同版本的包。当与钩子结合使用时，您可以编写一个函数，在所有 node_modules 中将一个版本的包替换为另一个版本。
我们可以创建一个名为`veros-magical-lodash`的新包并安装它:`pnpm install lodash@npm:veros-magical-lodash`这里有一个 pnpmfile.js 来做这个:
[![screenshot](img/ca7eeacfe2f8306095d212a5fa6ec239.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5xc5vk_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/283q6hizhyq9nodegte6.png)

# 弊端

由于 pnpm 使用的非标准节点模块结构，它有一些限制:

*   `package-lock.json`被忽略。因为 pnpm 创建了不同的文件结构，所以它不能使自己与 npm 的 lockfile 格式保持一致。
*   在使用 pnpm 的项目中执行时，Node.js 不能使用`--preserve-symlinks`标志。如果你开始在 pnpm 中工作并发现了一个真正的 bug，请向 [Github](https://github.com/pnpm/pnpm/issues/new) 上的页面报告。

# 见解

对于那些有依赖问题并希望回到 npm 的人来说， [npm 全局配置](https://docs.npmjs.com/misc/config#global-style)工作区可以帮助平整文件并解决一些运行时问题。

npm 的一个新项目 [Tink](https://github.com/npm/tink) 也在进行中，该项目旨在重塑 Node.js 和 npm 注册表之间的关系，允许您从 package-lock.json 文件定制依赖关系。

纱线也随着[纱线即插即用](https://github.com/arcanis/rfcs/blob/yarn-pnp/accepted/0000-plug-an-play.md)而发展。如果你很好奇，想尝试一下，[游乐场示例应用](https://github.com/yarnpkg/pnp-sample-app)是一个很好的起点。

总之，看起来理想的 pnpm 用户应该是一个经验丰富的开发人员和速度狂，对定制包和利用钩子获得特定结果的复杂性感到满意。如果您有兴趣将 pnpm 集成到您的项目中，并想了解更多，请访问 [pnpm 文档](https://pnpm.js.org/)。

我真的很喜欢写这篇文章和研究 pnpm 的核心概念。一如既往，欢迎提问、评论和反馈。感谢阅读！