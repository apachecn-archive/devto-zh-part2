# 使用 Go 和 Vue.js 构建照片源

> 原文：<https://dev.to/neo/build-a-photo-feed-with-go-and-vuejs-33b3>

你需要在你的机器上安装 Go 和 SQLite，以及 Go 和 JavaScript 的基础知识。

许多基于社交媒体的应用程序允许用户上传照片，这些照片通常以时间轴显示，供他们的追随者和其他人查看。在过去，你必须手动刷新你的订阅源才能看到上传到时间线上的新照片。然而，借助现代网络技术，您可以实时看到更新，而不必手动刷新页面。

在本文中，我们将考虑如何使用 Pusher Channels、GO 和 little Vue.js 构建实时照片馈送。 [Pusher Channels](https://pusher.com) 帮助您“使用我们托管的发布/订阅消息 API，在您的 web &移动应用中轻松构建可扩展的应用内通知、聊天、实时图表、地理跟踪等”

这是我们将构建的预览:

[![](../Images/9f5fc97c21d49def51e4885e5a42be1b.png)T2】](//images.ctfassets.net/1es3ne0caaid/5vVy2T7aY8qoOiCuA8oGQK/abdfe10038a2935fa4a894296db6630d/go-photo-feed-demo.gif)

## 先决条件

在我们开始构建应用程序之前，请确保您已经:

*   [Go](https://golang.org/) 编程语言的基础知识。
*   基础 JavaScript (Vue.js)知识。
*   Go(版本> = 0.10.x)安装在您的计算机上。查看[安装指南](https://golang.org/doc/install)。
*   计算机上安装的 SQLite(版本> = 3.x)。查看[安装指南](http://www.sqlitetutorial.net/download-install-sqlite/)。

让我们开始吧。

## 获取推焦渠道申请

第一步将是获得一个推广渠道的应用。我们需要应用程序凭证来使我们的实时特性工作。

去 Pusher 网站创建一个帐户。创建帐户后，您应该创建一个新的应用程序。遵循应用程序创建向导，然后您应该获得您的应用程序凭证，我们将在本文后面使用它。

[![](../Images/04a8c8a5c51d0d49eaaf5b46eb21d22d.png)T2】](//images.ctfassets.net/1es3ne0caaid/5mwD9r7FFmWugK6AyY08Ga/fdff5e30849724654dd10c6a5f3de29a/go-photo-feed-app-keys.png)

现在我们已经有了应用程序，让我们进入下一步

## 创建我们的 Go 应用

我们要做的下一件事是创建 Go 应用程序。在您的终端中，`cd`到您的`$GOPATH`并在那里创建一个新目录。

```
 $ cd $GOPATH/src
    $ mkdir gofoto
    $ cd gofoto 
```

Enter fullscreen mode Exit fullscreen mode

> 💡建议您将项目的源代码放在`src`子目录中(例如`$GOPATH/src/your_project`或`$GOPATH/src/github.com/your_github_username/your_project`)。

接下来，我们将创建一些目录来组织我们的应用程序:

```
 $ mkdir database
    $ mkdir public
    $ mkdir public/uploads 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`database`和`public`目录，并在公共目录中创建一个`uploads`目录。我们将在`database`目录中存储我们的数据库文件，我们将在`public`和`uploads`目录中保存我们的公共文件:HTML 和图像。在已经创建的`public`目录中创建一个新的`index.html`文件。

现在让我们为本文创建第一个(也是唯一一个)Go 文件。我们将把所有的源代码放在一个文件中，尽量保持简单。在项目根目录下创建一个`main.go`文件。

在文件中粘贴以下内容:

```
 <span class="hljs-keyword">package</span> main

    <span class="hljs-keyword">import</span> (
        <span class="hljs-string">"database/sql"</span>
        <span class="hljs-string">"io"</span>
        <span class="hljs-string">"net/http"</span>
        <span class="hljs-string">"os"</span>

        <span class="hljs-string">"github.com/labstack/echo"</span>
        <span class="hljs-string">"github.com/labstack/echo/middleware"</span>
        _ <span class="hljs-string">"github.com/mattn/go-sqlite3"</span>
        pusher <span class="hljs-string">"github.com/pusher/pusher-http-go"</span>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

上面我们已经导入了一些包，我们将需要我们的照片馈送工作。我们需要`database/sql`来运行 SQL 查询，需要`io`和`os`包来上传文件，需要`net/http`来获取 HTTP 状态代码。

我们有一些其他的外部包我们进口。`labstack/echo`包是我们将要使用的 [Echo 框架](https://github.com/labstack/echo)。我们还有用于 SQLite 的`mattn/go-sqlite3`包。最后，我们导入了`pusher/pusher-http-go`包，我们将使用它来触发推送通道的事件。

### 导入外部 Go 包

在我们继续之前，让我们使用我们的终端来获取这些包。运行下面的命令来获取包:

```
 $ go get github.com/labstack/echo
    $ go get github.com/labstack/echo/middleware
    $ go get github.com/mattn/go-sqlite3
    $ go get github.com/pusher/pusher-http-go 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，当完成软件包安装时，上面的命令不会返回任何确认输出。如果你想确认软件包确实被安装了，你可以检查一下`$GOPATH/src/github.com`目录。

现在我们已经引入了我们的包，让我们创建`main`函数。这是我们应用程序的入口点。在此功能中，我们将设置应用程序数据库、中间件和路由。

打开`main,go`文件，粘贴以下代码:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">main</span><span class="hljs-params">()</span></span> {
        db := initialiseDatabase(<span class="hljs-string">"database/database.sqlite"</span>)
        migrateDatabase(db)

        e := echo.New()

        e.Use(middleware.Logger())
        e.Use(middleware.Recover())

        e.File(<span class="hljs-string">"/"</span>, <span class="hljs-string">"public/index.html"</span>)
        e.GET(<span class="hljs-string">"/photos"</span>, getPhotos(db))
        e.POST(<span class="hljs-string">"/photos"</span>, uploadPhoto(db))
        e.Static(<span class="hljs-string">"/uploads"</span>, <span class="hljs-string">"public/uploads"</span>)

        e.Logger.Fatal(e.Start(<span class="hljs-string">":9000"</span>))
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们使用数据库文件的文件路径实例化了我们的数据库。如果 SQLite 文件尚不存在，这将创建它。然后我们运行迁移数据库的`migrateDatabase`函数。

接下来，我们实例化 Echo，然后注册一些中间件。[日志记录中间件](https://echo.labstack.com/middleware/logger)有助于记录关于 HTTP 请求的信息，而[恢复中间件](https://echo.labstack.com/middleware/recover)“从链中任何地方的混乱中恢复，打印堆栈跟踪并处理对集中式 [HTTPErrorHandler](https://echo.labstack.com/guide/customization#http-error-handler) 的控制。”

然后，我们设置一些路由来处理我们的请求。第一个处理程序是`File`处理程序。我们用它来服务于`index.html`文件。这将是从前端到应用程序的入口点。我们还有接受`POST`和`GET`请求的`/photos`路线。我们需要这些路由充当用于上传和显示照片的 API 端点。最后的处理程序是`Static`。我们用它来返回存储在`/uploads`目录中的静态文件。

我们最后使用`e.Start`来启动运行在端口 9000 上的 Go web 服务器。端口不是一成不变的，您可以选择任何您喜欢的可用和未使用的端口。

在这一点上，我们还没有创建我们在`main`函数中引用的大部分函数，所以让我们现在就这样做。

### 创建我们的数据库管理功能

在`main`函数中，我们引用了一个`initialiseDatabase`和`migrateDatabase`函数。让我们现在创建它们。在`main.go`文件中，将以下函数粘贴在`main`函数上方:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">initialiseDatabase</span><span class="hljs-params">(filepath <span class="hljs-keyword">string</span>)</span> *<span class="hljs-title">sql</span>.<span class="hljs-title">DB</span></span> {
        db, err := sql.Open(<span class="hljs-string">"sqlite3"</span>, filepath)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> || db == <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(<span class="hljs-string">"Error connecting to database"</span>)
        }

        <span class="hljs-keyword">return</span> db
    }

    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">migrateDatabase</span><span class="hljs-params">(db *sql.DB)</span></span> {
        sql := <span class="hljs-string">`
            CREATE TABLE IF NOT EXISTS photos(
                    id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
                    src VARCHAR NOT NULL
            );
       `</span>

        _, err := db.Exec(sql)
        <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
            <span class="hljs-built_in">panic</span>(err)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在`initialiseDatabase`函数中，我们使用数据库文件创建一个 SQLite 数据库实例，并返回该实例。在`migrateDatabase`函数中，我们使用前面函数中返回的数据库实例来执行迁移 SQL。

让我们为照片和照片集创建数据结构。

### 创建我们的数据结构

我们要做的下一件事是为我们的对象类型创建数据结构。我们将创建一个`Photo`结构和一个`PhotoCollection`结构。`Photo`结构将定义如何表示一张典型的照片，而`PhotoCollection`将定义如何表示一组照片。

打开`main.go`文件，将下面的代码粘贴到`initialiseDatabase`函数上面:

```
 <span class="hljs-keyword">type</span> Photo <span class="hljs-keyword">struct</span> {
        ID  <span class="hljs-keyword">int64</span>  <span class="hljs-string">`json:"id"`</span>
        Src <span class="hljs-keyword">string</span> <span class="hljs-string">`json:"src"`</span>
    }

    <span class="hljs-keyword">type</span> PhotoCollection <span class="hljs-keyword">struct</span> {
        Photos []Photo <span class="hljs-string">`json:"items"`</span>
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建我们的路线处理函数

接下来，让我们为我们的路线创建函数。打开`main.go`文件并在其中粘贴以下文件:

```
 <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">getPhotos</span><span class="hljs-params">(db *sql.DB)</span> <span class="hljs-title">echo</span>.<span class="hljs-title">HandlerFunc</span></span> {
        <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            rows, err := db.Query(<span class="hljs-string">"SELECT * FROM photos"</span>)
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            <span class="hljs-keyword">defer</span> rows.Close()

            result := PhotoCollection{}

            <span class="hljs-keyword">for</span> rows.Next() {
                photo := Photo{}

                err2 := rows.Scan(&photo.ID, &photo.Src)
                <span class="hljs-keyword">if</span> err2 != <span class="hljs-literal">nil</span> {
                    <span class="hljs-built_in">panic</span>(err2)
                }

                result.Photos = <span class="hljs-built_in">append</span>(result.Photos, photo)
            }

            <span class="hljs-keyword">return</span> c.JSON(http.StatusOK, result)
        }
    }

    <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">uploadPhoto</span><span class="hljs-params">(db *sql.DB)</span> <span class="hljs-title">echo</span>.<span class="hljs-title">HandlerFunc</span></span> {
        <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">func</span><span class="hljs-params">(c echo.Context)</span> <span class="hljs-title">error</span></span> {
            file, err := c.FormFile(<span class="hljs-string">"file"</span>)
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-keyword">return</span> err
            }

            src, err := file.Open()
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-keyword">return</span> err
            }

            <span class="hljs-keyword">defer</span> src.Close()

            filePath := <span class="hljs-string">"./public/uploads/"</span> + file.Filename
            fileSrc := <span class="hljs-string">"http://127.0.0.1:9000/uploads/"</span> + file.Filename

            dst, err := os.Create(filePath)
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            <span class="hljs-keyword">defer</span> dst.Close()

            <span class="hljs-keyword">if</span> _, err = io.Copy(dst, src); err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            stmt, err := db.Prepare(<span class="hljs-string">"INSERT INTO photos (src) VALUES(?)"</span>)
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            <span class="hljs-keyword">defer</span> stmt.Close()

            result, err := stmt.Exec(fileSrc)
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            insertedId, err := result.LastInsertId()
            <span class="hljs-keyword">if</span> err != <span class="hljs-literal">nil</span> {
                <span class="hljs-built_in">panic</span>(err)
            }

            photo := Photo{
                Src: fileSrc,
                ID:  insertedId,
            }

            <span class="hljs-keyword">return</span> c.JSON(http.StatusOK, photo)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在`getPhotos`方法中，我们只是运行查询从数据库中获取所有照片，并将它们作为 JSON 响应返回给客户端。在`uploadPhoto`方法中，我们首先获取要上传的文件，然后将它们上传到服务器，然后我们运行查询，用新上传的照片在`photos`表中插入一条新记录。我们还从该函数返回一个 JSON 响应。

### 为我们的 Go 应用程序添加实时支持

我们要做的下一件事是当一张新照片上传到服务器时触发一个事件。为此，我们将使用 [Pusher Go HTTP 库](https://github.com/pusher/pusher-http-go)。

在`main.go`文件中，将以下内容粘贴到`Photo`和`PhotoCollection`的类型定义上方:

```
 <span class="hljs-keyword">var</span> client = pusher.Client{
        AppId:   <span class="hljs-string">"PUSHER_APP_ID"</span>,
        Key:     <span class="hljs-string">"PUSHER_APP_KEY"</span>,
        Secret:  <span class="hljs-string">"PUSHER_APP_SECRET"</span>,
        Cluster: <span class="hljs-string">"PUSHER_APP_CLUSTER"</span>,
        Secure:  <span class="hljs-literal">true</span>,
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新的 Pusher 客户端实例。然后，我们可以使用这个实例来触发对我们想要的不同通道的通知。记得用之前创建 Pusher 应用程序时提供的密钥替换`PUSHER_APP_*`密钥。

接下来，转到`main.go`文件中的`uploadPhoto`函数，就在函数底部的`return`语句之前，粘贴以下代码:

这是当一张新照片上传到我们的应用程序时触发新事件的代码。

这就是我们 Go 应用程序的全部内容。此时，您可以构建您的应用程序，并使用`go build`命令将其编译成二进制文件。然而，对于本教程，我们将只是暂时运行二进制:

```
 $ go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/0f461b4d9d0fb08bf83d2c94f91188ea.png)T2】](//images.ctfassets.net/1es3ne0caaid/3DPeaIV05GMOGMSUouAaO/ce811f6f30da4b86c8c659f8637fdc3e/go-photo-feed-go-run.png)

## 建设我们的前端

接下来我们要做的是构建我们的前端。我们将使用 [Vue.js 框架](https://vuejs.org/)和 [Axios 库](https://github.com/axios/axios)来发送请求。

打开`index.html`文件，在其中粘贴以下代码:

```
 <span class="hljs-meta"><!doctype html></span>
    <span class="hljs-tag"><<span class="hljs-name">html</span> <span class="hljs-attr">lang</span>=<span class="hljs-string">"en"</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">head</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">charset</span>=<span class="hljs-string">"utf-8"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"viewport"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"width=device-width, initial-scale=1, shrink-to-fit=no"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">link</span> <span class="hljs-attr">rel</span>=<span class="hljs-string">"stylesheet"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">title</span>></span>Photo Feed<span class="hljs-tag"></<span class="hljs-name">title</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">style</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"text/css"</span>></span><span class="css">
            <span class="hljs-selector-id">#photoFile</span> { <span class="hljs-attribute">display</span>: none; }
            <span class="hljs-selector-id">#app</span> <span class="hljs-selector-tag">img</span> { <span class="hljs-attribute">max-width</span>: <span class="hljs-number">100%</span>; }
            <span class="hljs-selector-class">.image-row</span> { <span class="hljs-attribute">margin</span>: <span class="hljs-number">20px</span> <span class="hljs-number">0</span>; }
            <span class="hljs-selector-class">.image-row</span> <span class="hljs-selector-class">.thumbnail</span> { <span class="hljs-attribute">padding</span>: <span class="hljs-number">2px</span>; <span class="hljs-attribute">border</span>: <span class="hljs-number">1px</span> solid <span class="hljs-number">#d9d9d9</span>; }
        </span><span class="hljs-tag"></<span class="hljs-name">style</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">head</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">body</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"app"</span>></span>

            <span class="hljs-tag"><<span class="hljs-name">nav</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"navbar navbar-expand-lg navbar-light bg-light"</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">a</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"navbar-brand"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"#"</span>></span>GoFoto<span class="hljs-tag"></<span class="hljs-name">a</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">div</span>></span>
                    <span class="hljs-tag"><<span class="hljs-name">ul</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"navbar-nav mr-auto"</span>></span>
                        <span class="hljs-tag"><<span class="hljs-name">li</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"nav-item active"</span>></span>
                            <span class="hljs-tag"><<span class="hljs-name">a</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"nav-link"</span> <span class="hljs-attr">v-on:click</span>=<span class="hljs-string">"filePicker"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"#"</span>></span>Upload<span class="hljs-tag"></<span class="hljs-name">a</span>></span>
                            <span class="hljs-tag"><<span class="hljs-name">input</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"file"</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"photoFile"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"myFiles"</span> @<span class="hljs-attr">change</span>=<span class="hljs-string">"upload"</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"file"</span> /></span>
                        <span class="hljs-tag"></<span class="hljs-name">li</span>></span>
                    <span class="hljs-tag"></<span class="hljs-name">ul</span>></span>
                <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">nav</span>></span>

            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"container"</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row justify-content-md-center"</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"loading"</span> <span class="hljs-attr">v-if</span>=<span class="hljs-string">"loading"</span>></span>
                    <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col-xs-12"</span>></span>
                        Loading photos...
                    <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
                <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row justify-content-md-center image-row"</span> <span class="hljs-attr">v-for</span>=<span class="hljs-string">"photo in photos"</span>></span>
                    <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col col-lg-4 col-md-6 col-xs-12"</span>></span>
                        <span class="hljs-tag"><<span class="hljs-name">img</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"thumbnail"</span> <span class="hljs-attr">:src</span>=<span class="hljs-string">"photo.src"</span> <span class="hljs-attr">alt</span>=<span class="hljs-string">""</span> /></span>
                    <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
                <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>

        <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"//js.pusher.com/4.0/pusher.min.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://unpkg.com/axios/dist/axios.min.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">body</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">html</span>></span> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的 HTML 文件中，我们已经定义了图片流的设计。我们使用 Bootstrap 4，并且在上面的 HTML 中包含了 CSS。我们还使用 Axios 库、Pusher 库和 Vue 框架。我们在 HTML 文档的底部包含了脚本的链接。

接下来让我们添加 Vue.js 代码。在 HTML 文件中，在结束的`body`标记之前添加以下代码:

```
 <script type=<span class="hljs-string">"text/javascript"</span>>
        <span class="hljs-keyword">new</span> Vue({
            <span class="hljs-attr">el</span>: <span class="hljs-string">'#app'</span>,
            <span class="hljs-attr">data</span>: {
                <span class="hljs-attr">photos</span>: [],
                <span class="hljs-attr">loading</span>: <span class="hljs-literal">true</span>,
            },
            mounted() {
                <span class="hljs-keyword">const</span> pusher = <span class="hljs-keyword">new</span> Pusher(<span class="hljs-string">'PUSHER_APP_KEY'</span>, {
                    <span class="hljs-attr">cluster</span>: <span class="hljs-string">'PUSHER_APP_CLUSTER'</span>,
                    <span class="hljs-attr">encrypted</span>: <span class="hljs-literal">true</span>
                });

                <span class="hljs-keyword">let</span> channel = pusher.subscribe(<span class="hljs-string">'photo-stream'</span>)

                channel.bind(<span class="hljs-string">'new-photo'</span>, data => <span class="hljs-keyword">this</span>.photos.unshift(data));

                axios.get(<span class="hljs-string">'/photos'</span>).then(<span class="hljs-function"><span class="hljs-params">res</span> =></span> {
                    <span class="hljs-keyword">this</span>.loading = <span class="hljs-literal">false</span>
                    <span class="hljs-keyword">this</span>.photos = res.data.items ? res.data.items : []
                })
            },
            <span class="hljs-attr">methods</span>: {
                <span class="hljs-attr">filePicker</span>: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
                    <span class="hljs-keyword">let</span> elem = <span class="hljs-built_in">document</span>.getElementById(<span class="hljs-string">'photoFile'</span>);

                    <span class="hljs-keyword">if</span> (elem && <span class="hljs-built_in">document</span>.createEvent) {
                        <span class="hljs-keyword">let</span> evt = <span class="hljs-built_in">document</span>.createEvent(<span class="hljs-string">"MouseEvents"</span>);
                        evt.initEvent(<span class="hljs-string">"click"</span>, <span class="hljs-literal">true</span>, <span class="hljs-literal">false</span>);
                        elem.dispatchEvent(evt);
                    }
                },
                <span class="hljs-attr">upload</span>: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
                    <span class="hljs-keyword">let</span> data = <span class="hljs-keyword">new</span> FormData();
                    data.append(<span class="hljs-string">'file'</span>, <span class="hljs-keyword">this</span>.$refs.myFiles.files[<span class="hljs-number">0</span>]);

                    axios.post(<span class="hljs-string">'/photos'</span>, data).then(<span class="hljs-function"><span class="hljs-params">res</span> =></span> <span class="hljs-built_in">console</span>.log(res))
                }
            }
        });
    <span class="xml"><span class="hljs-tag"></<span class="hljs-name">script</span>></span></span> 
```

Enter fullscreen mode Exit fullscreen mode

上面我们创建了一个 Vue 实例并存储了属性`photos`和`loading`。`photos`属性存储照片列表，`loading`只保存一个布尔值，表明照片是否正在加载。

在`mounted`方法中，我们创建了 Pusher 库的一个实例。然后我们在`photo-stream`频道收听`new-photo`事件。当事件被触发时，我们将事件中的新照片添加到`photos`列表中。我们还向`/photos`发送 GET 请求，从 API 中获取所有照片。将`PUSHER_APP_*`键更换为推杆仪表板上的键。

在`methods`属性中，我们添加了一些方法。当按下 UI 上的“上传”按钮时，触发`filePicker`。它触发一个文件选择器，允许用户上传照片。`upload`方法获取上传的文件，并将带有该文件的 POST 请求发送给 API 进行处理。

这是所有的前端，你可以保存文件，并前往您的网页浏览器。请访问 http://127.0.0.1:9000 查看您的应用程序的运行情况。

这是它将再次呈现的样子:

[![](../Images/9f5fc97c21d49def51e4885e5a42be1b.png)T2】](//images.ctfassets.net/1es3ne0caaid/5vVy2T7aY8qoOiCuA8oGQK/abdfe10038a2935fa4a894296db6630d/go-photo-feed-demo.gif)

## 结论

在本文中，我们已经演示了如何在 Go 应用程序中使用推送通道来为应用程序提供实时特性。从上面的代码示例可以看出，使用推送通道非常容易。查看[文档](https://pusher.com/docs)了解使用推送通道向用户提供实时功能的其他方式。

这个应用程序的源代码可以在 [GitHub](https://github.com/neoighodaro/realtime-photofeed-pusher-go) 上找到。

这篇文章最初出现在 [Pusher 博客](https://pusher.com/tutorials/photo-feed-go-vuejs)上。