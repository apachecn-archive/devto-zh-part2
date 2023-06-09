# Haskell Yesod 的真实世界示例

> 原文：<https://dev.to/tzemanovic/realworld-example-with-haskell-yesod-53b8>

这是使用 Haskell 编写的强大的 [Yesod 框架](https://www.yesodweb.com/)构建的[现实世界](https://realworld.io/)后端的[示例实现](https://github.com/tzemanovic/haskell-yesod-realworld-example-app)的概述。

这个项目本质上是一个数据库的 RESTful API，尽管 Yesod 能做的远不止这些，但在这个过程中你仍然会得到很多有用的东西。例如，Yesod 附带了一些用于常见任务的助手命令(例如，您可以使用`yesod add-handler`添加一个新的处理程序),并且您可以使用[堆栈模板](https://docs.haskellstack.org/en/stable/GUIDE/#templates)之一启动一个新项目。我已经用`stack new yesod-sqlite`开始了这个项目，它搭建了带有 SQLite 数据库连接的应用程序，它的模式是从[持久实体文件](https://github.com/tzemanovic/haskell-yesod-realworld-example-app/blob/8411fa8a2ae8c2061323e58eaedd611186cf1394/app/config/models)创建的。脚手架项目的设置包括:

*   使用 ghcid 的开发服务器设置，具有自动重建和快速重新加载功能
*   为 Cabal 包格式寻找更好的替代方案
*   使用 [hspec](https://hackage.haskell.org/package/hspec) 的行为规范
*   使用[快速日志记录器](https://hackage.haskell.org/package/fast-logger)对请求和数据库查询进行快速和可扩展的日志记录
*   来自 [monad-logger](https://hackage.haskell.org/package/monad-logger) 的日志函数，包括日志条目中的源代码位置
*   最快的[曲速](https://hackage.haskell.org/package/warp)网络服务器
*   [classy-prelude](https://hackage.haskell.org/package/classy-prelude) 作为替代前奏，删除所有部分功能
*   [持久](https://hackage.haskell.org/package/persistent)用于类型安全的数据库无关数据建模和查询，并在开发中自动迁移
*   [aeson](https://hackage.haskell.org/package/aeson) 用于快速 JSON 解析和编码
*   [科特](https://hackage.haskell.org/package/keter)部署系统

如果你想了解更多关于 Yesod 的知识，可以免费阅读优秀的[用 Haskell 和 Yesod 开发 Web 应用](https://www.yesodweb.com/book)。此项目中使用的其他一些依赖项有:

*   [esqueleto](https://hackage.haskell.org/package/esqueleto) -持久后端 SQL 查询的类型安全 EDSL
*   [forma](https://hackage.haskell.org/package/forma) -解析并验证表单输入
*   [jwt](https://hackage.haskell.org/package/jwt) - JSON Web Token (JWT)解码和编码
*   [除草机](https://hackage.haskell.org/package/weeder) -检测死代码

## 项目结构

```
app
├── app
├── package.yaml
│   ├── main.hs                     "The main entry point for production build."
│   ├── devel.hs                    "The main for development server."
│   └── DevelMain.hs                "The main for running inside GHCi."
├── config
│   ├── models                      "Persistent entity file with the database schema declaration."
│   ├── routes                      "Routes declaration for our RESTful API."
│   ├── settings.yml                "App runtime settings."
│   └── test-settings.yml           "Settings overrides for tests."
├── src
│   ├── Application.hs              "Loads the settings to initalize the app with a HTTP manager,"
│   │                               "logger and database connection pool."
│   │
│   ├── Foundation.hs               "Declares the foundation 'App' datatype which is accessible"
│   │                               "from every handler, type for routes loaded from `config/routes`"
│   │                               "and authentication requirements for each route."
│   │
│   ├── Settings.hs                 "Declares and loads the app settings, which can be loaded from"
│   │                               "various sources."
│   │
│   ├── Import.hs                   "Commonly used module imports grouped for convenience."
│   ├── Model.hs                    "Generates types for models loaded from `config/models`."
│   ├── Database.hs                 "Database queries."
│   ├── Pagination.hs
│   ├── Handler                     "Handlers for the RESTful API."
│   │   ├── Articles.hs
│   │   ├── Profiles.hs
│   │   └── User.hs
│   ├── Database                    "Helpers for dealing with database."
│   │   └── Persist
│   │       ├── Extended.hs
│   │       └── Types
│   │           ├── Email           "Persistent field type for email, which is"
│   │           │   └── Internal.hs "stored in a case-insensitive string."
│   │           ├── Email.hs
│   │           ├── Password        "Persistent field type for password,"
│   │           │   └── Internal.hs "stored as a hash."
│   │           └── Password.hs
│   ├── Auth                        "Authentication using JWT token."
│   │   └── JWT.hs
│   └── Web                         "User input validation."
│       └── Forma
│           └── Extended.hs
├── test                            "The specs for user and profiles handlers."
│   ├── Handler
│   │   ├── ProfilesSpec.hs
│   │   └── UserSpec.hs
│   ├── Spec.hs
│   └── TestImport.hs 
```

Enter fullscreen mode Exit fullscreen mode

## 一些笔记

处理程序是根据 [API 规范](https://github.com/gothinkster/realworld/tree/master/api)实现的:

*   `Profiles`模块是最简单的模块，其处理程序使用持久查询
*   `User`处理程序处理使用 forma 库解析和验证的用户输入
*   `Articles`处理程序包含更复杂的文章提要查询和分页。因为他们的查询处理多个表，所以我联系了 esqueleto 库，它提供了一个很好的 eDSL(嵌入在 Haskell 中的 SQL 语言)来查询数据库。