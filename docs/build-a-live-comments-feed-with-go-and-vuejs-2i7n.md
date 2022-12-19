# 使用 Go 和 Vue.js 构建实时评论源

> 原文：<https://dev.to/neo/build-a-live-comments-feed-with-go-and-vuejs-2i7n>

> 你需要在你的机器上安装 Go 和 SQLite。Go 和 JavaScript 的基础知识会有帮助。

互联网是各种社会活动的温床，因为它扩大了交流的可能性。为了保持 web 应用程序的社会性和趣味性，为用户提供一个或多个交互界面是非常重要的。一个这样的界面是评论部分。

评论区是用户可以讨论他们有权访问的主题(帖子、视频、图片)的地方。过去，一个用户要看到另一个用户的评论，必须刷新浏览器窗口。然而，现在有了实时评论，我们可以自动实时获取评论。本文将介绍如何使用 Pusher 构建实时评论。

到本文结束时，我们将构建一个如下所示的应用程序:

[![](../Images/5c451b3f82fc0e042c63c89385e37600.png)T2】](//images.ctfassets.net/1es3ne0caaid/3dhebYl8aAm4cCME4Os2ys/4d39432a0bd39156cf8556c15649a2f1/go-comments-demo.gif)

## 要求

要阅读本文，您需要以下内容:

*   Go(版本> = 0.10.x)安装在您的计算机上。以下是你如何安装 Go 的方法。
*   安装在您机器上的 SQLite (v3.x)。[安装指南](http://www.sqlitetutorial.net/download-install-sqlite/)。
*   Go 编程语言的基础知识。
*   JavaScript 基础知识(ES6)。
*   Vue.js 的基础知识。

## 获取推焦渠道申请

第一步将是获得一个推广渠道的应用。我们需要应用程序凭证来使我们的实时特性工作。

去 Pusher 网站创建一个帐户。创建帐户后，您应该创建一个新的应用程序。遵循应用程序创建向导，然后您应该获得您的应用程序凭证，我们将在本文后面使用它。

[![](../Images/b8ac2f2ee7d1b8b5cebe1d93f03e532c.png)T2】](//images.ctfassets.net/1es3ne0caaid/2h3e4hp58cIW6acMc8Mgy0/dfeac8f3cdffb41174e93ad567233cd0/go-comments-app-keys.png)

现在我们有了应用程序，让我们进入下一步。

## 设置代码库

让我们首先导航到位于`$GOPATH`中的`src`目录。然后，我们将在那里为我们的应用程序创建一个新目录。

```
 $ cd $GOPATH/src
    $ mkdir go-realtime-comments
    $ cd go-realtime-comments 
```

Enter fullscreen mode Exit fullscreen mode

在这个目录下创建一个`comments.go`文件。

在我们编写代码之前，我们需要导入几个 Go 包来帮助运行我们的项目。我们将安装 [Echo 框架](https://echo.labstack.com)和 [SQLite 包](https://github.com/mattn/go-sqlite3)。运行以下命令来获取包:

```
 $ go get github.com/labstack/echo
    $ go get github.com/labstack/echo/middleware
    $ go get github.com/mattn/go-sqlite3 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你使用 Windows 并且你遇到错误‘cc . exe:对不起，未实现:64 位模式未编译进来’，那么你需要一个 Windows gcc 端口，比如[https://sourceforge.net/projects/mingw-w64/](https://sourceforge.net/projects/mingw-w64/)。另见此 [GitHub 问题](https://github.com/mattn/go-sqlite3/issues/297)。

使用您最喜欢的编辑器，打开`comments.go`文件并粘贴以下代码行:

```
 <span class="hljs-keyword">package</span> main

    <span class="hljs-keyword">import</span> (
        <span class="hljs-comment">// "database/sql"</span>

        <span class="hljs-string">"github.com/labstack/echo"</span>
        <span class="hljs-string">"github.com/labstack/echo/middleware"</span>
        <span class="hljs-comment">// _ "github.com/mattn/go-sqlite3"</span>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

## 配置数据库和路线

每个 Go 应用都必须有一个`main`函数。这是应用程序执行的起点，所以让我们创建我们的`main`函数:

在`comments.go`文件中，在导入下面添加以下内容:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">main</span><span class="hljs-params">()</span></span> {

        <span class="hljs-comment">// Echo instance</span>
        e := echo.New()

        <span class="hljs-comment">// Middleware</span>
        e.Use(middleware.Logger())
        e.Use(middleware.Recover())

        <span class="hljs-comment">// Define the HTTP routes</span>
        e.GET(<span class="hljs-string">"/comments"</span>, <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            <span class="hljs-keyword">return</span> c.JSON(<span class="hljs-number">200</span>, <span class="hljs-string">"GET Comments"</span>)
        })

        e.POST(<span class="hljs-string">"/comment"</span>, <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            <span class="hljs-keyword">return</span> c.JSON(<span class="hljs-number">200</span>, <span class="hljs-string">"POST a new Comment"</span>)
        })

        <span class="hljs-comment">// Start server</span>
        e.Logger.Fatal(e.Start(<span class="hljs-string">":9000"</span>))
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 main 函数中，我们定义了一些基本的 route handler 函数，这些函数基本上根据请求将硬编码的文本返回给浏览器。最后一行将使用 Echo 的 start 方法启动 Go 的标准 HTTP 服务器，并在端口 9000 监听请求。

我们可以通过运行应用程序并使用 [Postman](https://www.getpostman.com/) 发出一些请求来测试应用程序在这个阶段是否工作。

下面是运行应用程序的方法:

```
 $ go run ./comments.go 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 Postman 发送 HTTP 请求。下面是一个使用 Postman 的 GET 请求示例:

[![](../Images/48527dfadb78ee8d6e0ae75e4d2195c2.png)T2】](//images.ctfassets.net/1es3ne0caaid/1hOAHfzR8C2SocUciKSaEi/de4455b61abfaed1da7f612a6b1c75f6/go-comments-get-example.png)

邮递员的邮寄请求:

[![](../Images/f2125c56d385d1a6da9cb8a2c0652d3c.png)T2】](//images.ctfassets.net/1es3ne0caaid/1uCCDXGXBeus4mSKgSKYoi/a66e13b4921d54b799a3ceae40c2b4d1/go-comments-post-example.png)

我们将创建一个初始化数据库的函数，为此我们需要 SQL 和 SQLite3 驱动程序。我们已经将它们添加到了`import`语句中，所以取消对它们的注释。我们还将创建一个函数，该函数将使用函数中定义的数据库模式来迁移数据库。

打开`comments.go`文件，在`main`函数前粘贴以下代码:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">initDB</span><span class="hljs-params">(filepath <span class="hljs-keyword">string</span>)</span> *<span class="hljs-title">sql</span>.<span class="hljs-title">DB</span></span> {
        db, err := sql.Open(<span class="hljs-string">"sqlite3"</span>, filepath)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }

        <span class="hljs-keyword">if</span> db == <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(<span class="hljs-string">"db nil"</span>)
        }
        <span class="hljs-keyword">return</span> db
    }

    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">migrate</span><span class="hljs-params">(db *sql.DB)</span></span> {
        sql := <span class="hljs-string">`
        CREATE TABLE IF NOT EXISTS comments(
                id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
                name VARCHAR NOT NULL,
                email VARCHAR NOT NULL,
                comment VARCHAR NOT NULL
        );
       `</span>
        _, err := db.Exec(sql)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将以下代码添加到`main`函数的顶部:

```
 <span class="hljs-comment">// [...]</span>

    <span class="hljs-comment">// Initialize the database</span>
    db := initDB(<span class="hljs-string">"storage.db"</span>)
    migrate(db)

    <span class="hljs-comment">// [...]</span> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以通过运行应用程序来检查这些函数是否被调用以及数据库是否在执行过程中被创建:

```
 go run comments.go 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️:如果你已经运行了 Go 应用程序，你需要使用键盘上的 ctrl+c 终止这个进程，然后重启它来查看变化。

当应用程序第一次运行时，如果以前不存在一个`storage.db`文件，将在工作目录中创建该文件。

## 设置经手人

我们已经测试了我们的应用程序监听指定的端口 9000，并按照我们的配置处理 HTTP 请求。然而，当前的处理函数只是将硬编码的文本返回给浏览器，所以让我们创建新的处理函数来处理对路由的响应。

在根目录下创建一个名为`handlers` :
的新文件夹

```
 $ mkdir handlers
    $ cd handlers 
```

Enter fullscreen mode Exit fullscreen mode

接下来创建一个`handlers.go`文件并粘贴以下内容:

```
 <span class="hljs-keyword">package</span> handlers

    <span class="hljs-keyword">import</span> (
        <span class="hljs-string">"database/sql"</span>
        <span class="hljs-string">"go-realtime-comments/models"</span>
        <span class="hljs-string">"net/http"</span>
        <span class="hljs-string">"github.com/labstack/echo"</span>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要返回到`comments.go`文件并导入处理程序包:

```
 import (
        "go-realtime-comments/handlers"

        // [...]
    ) 
```

Enter fullscreen mode Exit fullscreen mode

在同一文件中，用以下内容替换之前的路线定义:

```
 <span class="hljs-comment">// [...]</span>

    <span class="hljs-comment">// Define the HTTP routes</span>
    e.File(<span class="hljs-string">"/"</span>, <span class="hljs-string">"public/index.html"</span>)
    e.GET(<span class="hljs-string">"/comments"</span>, handlers.GetComments(db))
    e.POST(<span class="hljs-string">"/comment"</span>, handlers.PushComment(db))

    <span class="hljs-comment">// [...]</span> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将下面的代码粘贴到导入语句下的`handlers.go`文件中:

```
 <span class="hljs-keyword">type</span> H <span class="hljs-keyword">map</span>[<span class="hljs-keyword">string</span>]<span class="hljs-keyword">interface</span>{}

    <span class="hljs-comment">//GetComments handles the HTTP request that hits the /comments endpoint</span>
    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">GetComments</span><span class="hljs-params">(db *sql.DB)</span> <span class="hljs-title">echo</span>.<span class="hljs-title">HandlerFunc</span></span> {
        <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            <span class="hljs-keyword">return</span> c.JSON(http.StatusOK, models.GetComments(db))
        }
    }

    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">PushComment</span><span class="hljs-params">(db *sql.DB)</span> <span class="hljs-title">echo</span>.<span class="hljs-title">HandlerFunc</span></span> {
        <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            <span class="hljs-keyword">var</span> comment models.Comment

            c.Bind(&comment)

            id, err := models.PushComment(db, comment.Name, comment.Email, comment.Comment)
            <span class="hljs-keyword">if</span> err == <span class="hljs-literal">nil</span> {
                <span class="hljs-keyword">return</span> c.JSON(http.StatusCreated, H{
                    <span class="hljs-string">"created"</span>: id,
                })
            }

            <span class="hljs-keyword">return</span> err
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

`GetComments`函数从数据库获取并返回注释，而`PushComment`将注释保存到数据库并返回响应。

## 设置模型

为了创建模型包，我们需要在应用程序的根目录下创建一个新文件夹:

```
 $ mkdir models
    $ cd models 
```

Enter fullscreen mode Exit fullscreen mode

接下来创建一个`models.go`文件并粘贴以下代码:

```
 <span class="hljs-keyword">package</span> models

    <span class="hljs-keyword">import</span> (
        <span class="hljs-string">"database/sql"</span>
        _ <span class="hljs-string">"github.com/mattn/go-sqlite3"</span>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个注释`type`，它是一个具有四个字段的结构:

*   `ID` -评论的 ID。
*   `Name` -发表评论的用户的用户名。
*   `Email` -发表评论的用户的电子邮件。
*   `Comment` -评论。

在 Go 中，我们可以将元数据添加到变量中，方法是将它们放在反勾号中。我们可以用它来定义每个字段在转换成`JSON`时应该是什么样子。这也将有助于`c.Bind`函数在注册新评论时知道如何映射`JSON`数据。

让我们定义一下`Comment`和`CommentCollection`的结构。在`models.go`文件下面粘贴以下的导入:

```
 <span class="hljs-keyword">type</span> Comment <span class="hljs-keyword">struct</span> {
        ID      <span class="hljs-keyword">int</span>    <span class="hljs-string">`json:"id"`</span>
        Name    <span class="hljs-keyword">string</span> <span class="hljs-string">`json:"name"`</span>
        Email   <span class="hljs-keyword">string</span> <span class="hljs-string">`json:"email"`</span>
        Comment <span class="hljs-keyword">string</span> <span class="hljs-string">`json:"comment"`</span>
    }

    <span class="hljs-keyword">type</span> CommentCollection <span class="hljs-keyword">struct</span> {
        Comments []Comment <span class="hljs-string">`json:"items"`</span>
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将以下代码粘贴到结构之后:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">GetComments</span><span class="hljs-params">(db *sql.DB)</span> <span class="hljs-title">CommentCollection</span></span> {
        sql := <span class="hljs-string">"SELECT * FROM comments"</span>
        rows, err := db.Query(sql)

        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }

        <span class="hljs-keyword">defer</span> rows.Close()

        result := CommentCollection{}

        <span class="hljs-keyword">for</span> rows.Next() {

            comment := Comment{}
            err2 := rows.Scan(&comment.ID, &comment.Name, &comment.Email, &comment.Comment)
            <span class="hljs-keyword">if</span> err2 != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err2)
            }

            result.Comments = <span class="hljs-built_in">append</span>(result.Comments, comment)
        }

        <span class="hljs-keyword">return</span> result
    } 
```

Enter fullscreen mode Exit fullscreen mode

`GetComments`函数负责从数据库中检索所有可用的注释，并将它们作为我们定义的`CommentCollection`的实例返回。

接下来，将下面的代码粘贴到上面的代码下面:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">PushComment</span><span class="hljs-params">(db *sql.DB, name <span class="hljs-keyword">string</span>, email <span class="hljs-keyword">string</span>, comment <span class="hljs-keyword">string</span>)</span> <span class="hljs-params">(<span class="hljs-keyword">int64</span>, error)</span></span> {
        sql := <span class="hljs-string">"INSERT INTO comments(name, email, comment) VALUES(?, ?, ?)"</span>
        stmt, err := db.Prepare(sql)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }

        <span class="hljs-keyword">defer</span> stmt.Close()

        result, err2 := stmt.Exec(name, email, comment)
        <span class="hljs-keyword">if</span> err2 != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err2)
        }

        <span class="hljs-keyword">return</span> result.LastInsertId()
    } 
```

Enter fullscreen mode Exit fullscreen mode

`PushComments`函数向数据库添加新的注释。

## 建筑前端

接下来，在应用程序的根目录下创建一个`public`文件夹，并在其中创建一个`index.html`文件。

打开`index.html`文件，粘贴以下代码:

```
 <span class="hljs-meta"></span>
    <span class="hljs-tag"><<span class="hljs-name">html</span> <span class="hljs-attr">lang</span>=<span class="hljs-string">"en"</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">head</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">charset</span>=<span class="hljs-string">"UTF-8"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"viewport"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"width=device-width, initial-scale=1.0"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">http-equiv</span>=<span class="hljs-string">"X-UA-Compatible"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"ie=edge"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">link</span> <span class="hljs-attr">rel</span>=<span class="hljs-string">"stylesheet"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css"</span> <span class="hljs-attr">integrity</span>=<span class="hljs-string">"sha384-PsH8R72JQ3SOdhVi3uxftmaW6Vc51MKb0q5P2rRUpPvrszuE4W1povHYgTpBfshb"</span> <span class="hljs-attr">crossorigin</span>=<span class="hljs-string">"anonymous"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">title</span>></span>Realtime comments<span class="hljs-tag"></<span class="hljs-name">title</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://unpkg.com/axios/dist/axios.min.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">style</span>></span><span class="css">
          @<span class="hljs-keyword">media</span> (min-width: <span class="hljs-number">48em</span>) {
            <span class="hljs-selector-tag">html</span> {
              <span class="hljs-attribute">font-size</span>: <span class="hljs-number">18px</span>;
            }
          }
          <span class="hljs-selector-tag">body</span> {
            <span class="hljs-attribute">font-family</span>: Georgia, <span class="hljs-string">"Times New Roman"</span>, Times, serif;
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#555</span>;
          }
          <span class="hljs-selector-tag">h1</span>, <span class="hljs-selector-class">.h1</span>, <span class="hljs-selector-tag">h2</span>, <span class="hljs-selector-class">.h2</span>, <span class="hljs-selector-tag">h3</span>, <span class="hljs-selector-class">.h3</span>, <span class="hljs-selector-tag">h4</span>, <span class="hljs-selector-class">.h4</span>, <span class="hljs-selector-tag">h5</span>, <span class="hljs-selector-class">.h5</span>, <span class="hljs-selector-tag">h6</span>, <span class="hljs-selector-class">.h6</span> {
            <span class="hljs-attribute">font-family</span>: <span class="hljs-string">"Helvetica Neue"</span>, Helvetica, Arial, sans-serif;
            <span class="hljs-attribute">font-weight</span>: <span class="hljs-number">400</span>;
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#333</span>;
          }
          <span class="hljs-selector-class">.blog-masthead</span> {
            <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">3rem</span>;
            <span class="hljs-attribute">background-color</span>: <span class="hljs-number">#428bca</span>;
            <span class="hljs-attribute">box-shadow</span>: inset <span class="hljs-number">0</span> -.<span class="hljs-number">1rem</span> .<span class="hljs-number">25rem</span> <span class="hljs-built_in">rgba</span>(0,0,0,.1);
          }
          <span class="hljs-selector-class">.nav-link</span> {
            <span class="hljs-attribute">position</span>: relative;
            <span class="hljs-attribute">padding</span>: <span class="hljs-number">1rem</span>;
            <span class="hljs-attribute">font-weight</span>: <span class="hljs-number">500</span>;
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#cdddeb</span>;
          }
          <span class="hljs-selector-class">.nav-link</span><span class="hljs-selector-pseudo">:hover</span>, <span class="hljs-selector-class">.nav-link</span><span class="hljs-selector-pseudo">:focus</span> {
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#fff</span>;
            <span class="hljs-attribute">background-color</span>: transparent;
          }
          <span class="hljs-selector-class">.nav-link</span><span class="hljs-selector-class">.active</span> {
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#fff</span>;
          }
          <span class="hljs-selector-class">.nav-link</span><span class="hljs-selector-class">.active</span><span class="hljs-selector-pseudo">::after</span> {
            <span class="hljs-attribute">position</span>: absolute;
            <span class="hljs-attribute">bottom</span>: <span class="hljs-number">0</span>;
            <span class="hljs-attribute">left</span>: <span class="hljs-number">50%</span>;
            <span class="hljs-attribute">width</span>: <span class="hljs-number">0</span>;
            <span class="hljs-attribute">height</span>: <span class="hljs-number">0</span>;
            <span class="hljs-attribute">margin-left</span>: -.<span class="hljs-number">3rem</span>;
            <span class="hljs-attribute">vertical-align</span>: middle;
            <span class="hljs-attribute">content</span>: <span class="hljs-string">""</span>;
            <span class="hljs-attribute">border-right</span>: .<span class="hljs-number">3rem</span> solid transparent;
            <span class="hljs-attribute">border-bottom</span>: .<span class="hljs-number">3rem</span> solid;
            <span class="hljs-attribute">border-left</span>: .<span class="hljs-number">3rem</span> solid transparent;
          }
          @<span class="hljs-keyword">media</span> (min-width: <span class="hljs-number">40em</span>) {
            <span class="hljs-selector-class">.blog-title</span> {
              <span class="hljs-attribute">font-size</span>: <span class="hljs-number">3.5rem</span>;
            }
          }
          <span class="hljs-selector-class">.sidebar-module</span> {
            <span class="hljs-attribute">padding</span>: <span class="hljs-number">1rem</span>;
          }
          <span class="hljs-selector-class">.sidebar-module-inset</span> {
            <span class="hljs-attribute">padding</span>: <span class="hljs-number">1rem</span>;
            <span class="hljs-attribute">background-color</span>: <span class="hljs-number">#f5f5f5</span>;
            <span class="hljs-attribute">border-radius</span>: .<span class="hljs-number">25rem</span>;
          }
          <span class="hljs-selector-class">.sidebar-module-inset</span> <span class="hljs-selector-tag">p</span><span class="hljs-selector-pseudo">:last-child</span>,
          <span class="hljs-selector-class">.sidebar-module-inset</span> <span class="hljs-selector-tag">ul</span><span class="hljs-selector-pseudo">:last-child</span>,
          <span class="hljs-selector-class">.sidebar-module-inset</span> <span class="hljs-selector-tag">ol</span><span class="hljs-selector-pseudo">:last-child</span> {
            <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">0</span>;
          }
          <span class="hljs-selector-class">.blog-post</span> {
            <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">4rem</span>;
          }
          <span class="hljs-selector-class">.blog-post-title</span> {
            <span class="hljs-attribute">margin-bottom</span>: .<span class="hljs-number">25rem</span>;
            <span class="hljs-attribute">font-size</span>: <span class="hljs-number">2.5rem</span>;
            <span class="hljs-attribute">text-align</span>: center;
          }
          <span class="hljs-selector-class">.blog-post-meta</span> {
            <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">1.25rem</span>;
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#999</span>;
            <span class="hljs-attribute">text-align</span>: center;
          }
          <span class="hljs-selector-class">.blog-footer</span> {
            <span class="hljs-attribute">padding</span>: <span class="hljs-number">2.5rem</span> <span class="hljs-number">0</span>;
            <span class="hljs-attribute">color</span>: <span class="hljs-number">#999</span>;
            <span class="hljs-attribute">text-align</span>: center;
            <span class="hljs-attribute">background-color</span>: <span class="hljs-number">#f9f9f9</span>;
            <span class="hljs-attribute">border-top</span>: .<span class="hljs-number">05rem</span> solid <span class="hljs-number">#e5e5e5</span>;
          }
          <span class="hljs-selector-class">.blog-footer</span> <span class="hljs-selector-tag">p</span><span class="hljs-selector-pseudo">:last-child</span> {
            <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">0</span>;
          }
          <span class="hljs-selector-tag">input</span>{
              <span class="hljs-attribute">width</span>: <span class="hljs-number">45%</span> <span class="hljs-meta">!important</span>;
              <span class="hljs-attribute">display</span>: inline-block <span class="hljs-meta">!important</span>;
          }
          <span class="hljs-selector-tag">textarea</span> {
              <span class="hljs-attribute">width</span>: <span class="hljs-number">90%</span>;
              <span class="hljs-attribute">height</span>: <span class="hljs-number">150px</span>;
              <span class="hljs-attribute">padding</span>: <span class="hljs-number">12px</span> <span class="hljs-number">20px</span>;
              <span class="hljs-attribute">box-sizing</span>: border-box;
              <span class="hljs-attribute">border</span>: <span class="hljs-number">2px</span> solid <span class="hljs-number">#ccc</span>;
              <span class="hljs-attribute">border-radius</span>: <span class="hljs-number">4px</span>;
              <span class="hljs-attribute">background-color</span>: <span class="hljs-number">#f8f8f8</span>;
              <span class="hljs-attribute">resize</span>: none;
          }
          <span class="hljs-selector-tag">textarea</span><span class="hljs-selector-pseudo">:focus</span>, <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">:focus</span>{
              <span class="hljs-attribute">outline</span>: none <span class="hljs-meta">!important</span>;
          }
          <span class="hljs-selector-id">#comment-section</span>{
            <span class="hljs-attribute">background</span>: <span class="hljs-built_in">rgb</span>(178, 191, 214); 
            <span class="hljs-attribute">padding</span>: <span class="hljs-number">0.5em</span> <span class="hljs-number">2em</span>; <span class="hljs-attribute">width</span>: <span class="hljs-number">90%</span>;
            <span class="hljs-attribute">margin</span>: <span class="hljs-number">10px</span> <span class="hljs-number">0</span>;
            <span class="hljs-attribute">border-radius</span>: <span class="hljs-number">15px</span>;
          }
          <span class="hljs-selector-id">#comment-section</span> > <span class="hljs-selector-tag">div</span> > <span class="hljs-selector-tag">p</span> {
            <span class="hljs-attribute">color</span>: black;
            <span class="hljs-attribute">display</span>:inline;
          }
          <span class="hljs-selector-tag">img</span>{
          <span class="hljs-attribute">border-radius</span>: <span class="hljs-number">50%</span>;
          <span class="hljs-attribute">float</span>: left;
          }
        </span><span class="hljs-tag"></<span class="hljs-name">style</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">head</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">body</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"app"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">header</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"blog-masthead"</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"container"</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">nav</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"nav"</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">a</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"nav-link active"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"#"</span>></span>Home<span class="hljs-tag"></<span class="hljs-name">a</span>></span>
                <span class="hljs-tag"></<span class="hljs-name">nav</span>></span>
              <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">header</span>></span>

          <span class="hljs-tag"><<span class="hljs-name">main</span> <span class="hljs-attr">role</span>=<span class="hljs-string">"main"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"container"</span>></span>

            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row"</span>></span>

              <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col-sm-12 blog-main"</span>></span>

                <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"blog-post"</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">h2</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"blog-post-title"</span>></span>Realtime Comments With Pusher<span class="hljs-tag"></<span class="hljs-name">h2</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">p</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"blog-post-meta"</span>></span>January 1, 2018 by <span class="hljs-tag"><<span class="hljs-name">a</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"#"</span>></span>Jordan<span class="hljs-tag"></<span class="hljs-name">a</span>></span><span class="hljs-tag"></<span class="hljs-name">p</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">p</span>></span>This blog post shows a few different types of content that's supported and styled with Bootstrap. Basic typography, images, and code are all supported.This blog post shows a few different types of content that's supported and styled with Bootstrap. Basic typography, images, and code are all supported
                  <span class="hljs-tag"></<span class="hljs-name">p</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"comment-section"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">form</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-signin"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">h5</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"comment"</span>></span>Comment<span class="hljs-tag"></<span class="hljs-name">h5</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">input</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"username"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"username"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-control"</span> <span class="hljs-attr">placeholder</span>=<span class="hljs-string">"John Doe"</span> <span class="hljs-attr">required</span> <span class="hljs-attr">autofocus</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">input</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"email"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"email"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-control"</span> <span class="hljs-attr">placeholder</span>=<span class="hljs-string">"Johndoe@gmail.com"</span> <span class="hljs-attr">required</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">textarea</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"comment"</span>></span><span class="hljs-tag"></<span class="hljs-name">textarea</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">button</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"btn btn-lg btn-primary"</span> @<span class="hljs-attr">click.prevent</span>=<span class="hljs-string">"sendComment"</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"submit"</span>></span>Comment<span class="hljs-tag"></<span class="hljs-name">button</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">form</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">br</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"comment-section"</span> <span class="hljs-attr">v-for</span>=<span class="hljs-string">"comment in comments"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">div</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">img</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"http://merritos.com/img/team/maleTeam.jpg"</span> <span class="hljs-attr">width</span>=<span class="hljs-string">"65px"</span> <span class="hljs-attr">height</span>=<span class="hljs-string">"65px"</span>></span>
               <span class="hljs-tag"><<span class="hljs-name">p</span>></span>&nbsp;&nbsp;{{comment.name}} &nbsp;<span class="hljs-tag">< {{<span class="hljs-attr">comment.email</span>}} ></span><span class="hljs-tag"></<span class="hljs-name">p</span>></span>
               <span class="hljs-tag"><<span class="hljs-name">hr</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">p</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"color:black"</span>></span>{{comment.comment}}<span class="hljs-tag"></<span class="hljs-name">p</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
                  <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
                <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
              <span class="hljs-tag"></<span class="hljs-name">div</span>></span>

            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>

          <span class="hljs-tag"></<span class="hljs-name">main</span>></span>

          <span class="hljs-tag"><<span class="hljs-name">footer</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"blog-footer"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">p</span>></span><span class="hljs-tag"><<span class="hljs-name">a</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"#"</span>></span>Back to top<span class="hljs-tag"></<span class="hljs-name">a</span>></span><span class="hljs-tag"></<span class="hljs-name">p</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">footer</span>></span>

        <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">body</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">html</span>></span> 
```

Enter fullscreen mode Exit fullscreen mode

现在，在同一个文件中，将以下代码粘贴到 HTML 的结束标记`body`之前:

```
 <script>
      <span class="hljs-keyword">var</span> app = <span class="hljs-keyword">new</span> Vue({
        <span class="hljs-attr">el</span>: <span class="hljs-string">'#app'</span>,
        <span class="hljs-attr">data</span>: {
          <span class="hljs-attr">comments</span> : []
        },
        <span class="hljs-attr">created</span>: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
          axios.get(<span class="hljs-string">'/comments'</span>).then(<span class="hljs-function"><span class="hljs-params">response</span> =></span> { 
            <span class="hljs-keyword">this</span>.comments = response.data.items ? response.data.items : [] 
          })
        },
        <span class="hljs-attr">methods</span>: {
            <span class="hljs-attr">sendComment</span>: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">index</span>) </span>{
              <span class="hljs-keyword">let</span> comment = {
                <span class="hljs-attr">name</span>: <span class="hljs-keyword">this</span>.$refs.username.value,
                <span class="hljs-attr">email</span>: <span class="hljs-keyword">this</span>.$refs.email.value,
                <span class="hljs-attr">comment</span>: <span class="hljs-keyword">this</span>.$refs.comment.value
              }

              axios.post(<span class="hljs-string">'/comment'</span>, comment).then(<span class="hljs-function"><span class="hljs-params">response</span> =></span> { 
                <span class="hljs-keyword">this</span>.$refs.username.value = <span class="hljs-string">''</span>,
                <span class="hljs-keyword">this</span>.$refs.email.value = <span class="hljs-string">''</span>,
                <span class="hljs-keyword">this</span>.$refs.comment.value = <span class="hljs-string">''</span>
              })
            }
        }
      })
    <<span class="hljs-regexp">/script></span> 
```

Enter fullscreen mode Exit fullscreen mode

上面是我们应用程序的 Vue.js 代码，这是它的功能总结:

*   我们实例化了一个包含所有可用注释的注释数组。
*   在`created()`方法中，我们使用 [Axios](https://www.npmjs.com/package/axios) 从 API 获取所有可用的注释，并将其存储在`comments`数组中。
*   在`sendComment`方法中，我们向 API 发送一个创建新的`comment`的请求。

我们可以在这个阶段构建我们的应用程序，并访问 [http://localhost:9000](http://localhost:9000) ，我们应该看到这个:

```
 $ go run comments.go 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用程序应该如下所示:

[![](../Images/b1fd95010d8c4221b0c601743cee0756.png)T2】](//images.ctfassets.net/1es3ne0caaid/2m7H3gsZCMIkEKGueSEuK/d014e440249b413da2ed8f509d91eae1/go-comments-homepage.png)

### 实时显示评论

我们需要做的下一件事是确保评论被实时显示。为此，我们需要在每次添加新评论时触发一个事件。我们将在后端使用 [Pusher Go 库](https://github.com/pusher/pusher-http-go)来完成这项工作。

要拉进推料机 Go 库，运行以下命令:

```
 $ go get github.com/pusher/pusher-http-go 
```

Enter fullscreen mode Exit fullscreen mode

接下来让我们导入库。在我们的`models.go`文件中，在 imports 语句中执行以下操作:

```
 <span class="hljs-keyword">package</span> models

    <span class="hljs-keyword">import</span> (
        <span class="hljs-comment">// [...]</span>

        pusher <span class="hljs-string">"github.com/pusher/pusher-http-go"</span>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

在同一个文件中，在`type`定义之前，粘贴以下代码:

```
 <span class="hljs-comment">// [...]</span>

    <span class="hljs-keyword">var</span> client = pusher.Client{
        AppId:   <span class="hljs-string">"PUSHER_APP_ID"</span>,
        Key:     <span class="hljs-string">"PUSHER_APP_KEY"</span>,
        Secret:  <span class="hljs-string">"PUSHER_APP_SECRET"</span>,
        Cluster: <span class="hljs-string">"PUSHER_APP_CLUSTER"</span>,
        Secure:  <span class="hljs-literal">true</span>,
    }

    <span class="hljs-comment">// [...]</span> 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们已经使用之前创建的应用程序中的凭证初始化了 Pusher 客户端。

> ⚠️用你的 Pusher 应用凭证替换`PUSHER_APP_*`键。

接下来，让我们在每次注释保存到数据库时触发一个事件。用以下代码替换`PushComment`函数:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">PushComment</span><span class="hljs-params">(db *sql.DB, name <span class="hljs-keyword">string</span>, email <span class="hljs-keyword">string</span>, comment <span class="hljs-keyword">string</span>)</span> <span class="hljs-params">(<span class="hljs-keyword">int64</span>, error)</span></span> {
        sql := <span class="hljs-string">"INSERT INTO comments(name, email, comment) VALUES(?, ?, ?)"</span>
        stmt, err := db.Prepare(sql)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }

        <span class="hljs-keyword">defer</span> stmt.Close()

        result, err2 := stmt.Exec(name, email, comment)
        <span class="hljs-keyword">if</span> err2 != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err2)
        }

        newComment := Comment{
            Name:    name,
            Email:   email,
            Comment: comment,
        }

        client.Trigger(<span class="hljs-string">"comment-channel"</span>, <span class="hljs-string">"new-comment"</span>, newComment)
        <span class="hljs-keyword">return</span> result.LastInsertId()
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这个新版本的函数中，我们创建了一个`newComment`对象，它保存了保存到数据库中的最后一条注释的信息。每当一个新的评论被创建时，我们将把它发送到推送通道`comment-channel`以在事件`new-comment`时被触发。

**在客户端上实时显示数据**为了接收评论，我们必须在前端代码中注册 [Pusher JavaScript 客户端](https://github.com/pusher/pusher-js)。将这行代码添加到 index.html 文件中 HTML 的 head 标签内:

```
 <script src="https://js.pusher.com/4.1/pusher.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将在`created()`生命周期钩子:
中注册一个 Pusher 实例

```
 created: function() {

        <span class="hljs-keyword">const</span> pusher = <span class="hljs-built_in">new</span> Pusher(<span class="hljs-string">'PUSHER_APP_KEY'</span>, {
            cluster: <span class="hljs-string">'PUSHER_APP_CLUSTER'</span>,
            encrypted: <span class="hljs-literal">true</span>
        });

        <span class="hljs-keyword">const</span> channel = pusher.subscribe(<span class="hljs-string">'comment-channel'</span>);

        channel.bind(<span class="hljs-string">'new-comment'</span>, data => {
          this.comments.push(data)
        });

        <span class="hljs-comment">// [...]    </span>
    } 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️将`PUSHER_APP_*`键替换为你的 Pusher 应用的凭证。

在上面的代码中，我们创建了一个 Pusher 实例，然后订阅了一个通道。在那个频道中，我们正在收听`new-comment`事件。

现在我们可以运行我们的应用程序:

```
 $ go run comments.go 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将 web 浏览器指向这个地址 [http://localhost:9000](http://localhost:9000) ，我们应该可以看到应用程序在运行:

[![](../Images/5c451b3f82fc0e042c63c89385e37600.png)T2】](//images.ctfassets.net/1es3ne0caaid/3dhebYl8aAm4cCME4Os2ys/4d39432a0bd39156cf8556c15649a2f1/go-comments-demo.gif)

## 结论

在本文中，我们研究了如何使用 Go、Vue.js 和 Pusher 通道构建实时评论系统。该应用的源代码可在 [GitHub 上获得。](https://github.com/neoighodaro/realtime-comments-go-pusher)

这篇文章最初出现在 [Pusher 博客](https://pusher.com/tutorials/live-comments-go-vuejs)上。