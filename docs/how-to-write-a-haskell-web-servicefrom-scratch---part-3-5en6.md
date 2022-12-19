# 如何编写 Haskell web 服务(从头开始)——第 3 部分

> 原文：<https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-3-5en6>

这是创建基于 Haskell 的 web 服务实践指南的第三部分。不需要以前了解 haskell。然而，对 RESTful web 服务的基本理解是假定的。本系列其他帖子:[第一部](https://dev.to/parambirs/how-to-write-a-dockerized-haskell-web-servicefrom-scratch---part-1-3m7c)，[第二部](https://dev.to/parambirs/how-to-write-a-haskell-web-servicefrom-scratch---part-2-6pi)。

# 3。使用 Scotty 运行一个基本的 Haskell web 应用程序

## 介绍[斯科蒂](https://github.com/scotty-web/scotty)

Haskell 有很多可用的 web 框架:Yesod、Snap、Scotty 等。我选择了 Scotty，因为它似乎更容易上手。

我们将编写一个简单的 web 服务来响应各种 HTTP 请求类型(GET、PUT、POST、DELETE)。我们将看到如何获取请求头、路径参数和表单字段，以及如何以纯文本、html 或 JSON 响应进行响应。

## 全面初始化

让我们为我们的 web 服务初始化一个 cabal 应用程序。我主要选择了默认选项。两个显著的例外包括:
许可证:9) MIT
源目录:2)服务器

```
% mkdir scotty-webapp-example
% cd scotty-webapp-example
% cabal sandbox init
Writing a default package environment file to
/Users/psingh/tmp/haskell/eac-articles/scotty-webapp-example/cabal.sandbox.config
Using an existing sandbox located at
/Users/psingh/tmp/haskell/eac-articles/scotty-webapp-example/.cabal-sandbox
% cabal init
Package name? [default: scotty-webapp-example]
Package version? [default: 0.1.0.0]
...
... 
```

## 编写服务器代码

因为我们之前告诉过`cabal`我们的可执行文件的主模块将是`Main.hs`，并且它将位于`server`文件夹中，所以让我们将`server/Main.hs`文件添加到我们的源代码中。

```
{-# LANGUAGE OverloadedStrings #-}
import Web.Scotty
import Network.HTTP.Types

main = scotty 3000 $ do
  get "/" $ do                         -- handle GET request on "/" URL
    text "This was a GET request!"     -- send 'text/plain' response
  delete "/" $ do
    html "This was a DELETE request!"  -- send 'text/html' response
  post "/" $ do
    text "This was a POST request!"
  put "/" $ do
    text "This was a PUT request!" 
```

您可能已经知道，这段代码将在端口 3000 上启动一个服务器，并且:

*   对于在`/`路径上的`GET`请求，服务器将用一个 HTML 响应来响应，内容为“这是一个 GET 请求！”
*   对于在`/`路径上的`DELETE`请求，服务器将用“纯文本”响应来响应，内容为“这是一个删除请求！”

## 添加依赖关系

让我们在 cabal 文件中为`scotty`和`http-types`库添加一个依赖项。

这是 scotty-webapp-example.cabal 文件的 build-depends 部分现在的样子:

```
build-depends: base >=4.8 && <4.9 
```

改为:

```
build-depends: base >=4.8 && <4.9
             , scotty
             , http-types 
```

接下来，运行`cabal install`将依赖项添加到您的沙箱中，然后运行`cabal run`来运行服务器。

```
% cabal install
Resolving dependencies…
Notice: installing into a sandbox located at
/Users/psingh/tmp/haskell/eac-articles/scotty-webapp-example/.cabal-sandbox
Configuring ansi-terminal-0.6.2.3…
Configuring appar-0.1.4…
…
…
% cabal run
Package has never been configured. Configuring with default flags. If this fails, please run configure manually.
Resolving dependencies…
Configuring scotty-webapp-example-0.1.0.0…
Preprocessing executable ‘scotty-webapp-example’ for
scotty-webapp-example-0.1.0.0…
[1 of 1] Compiling Main ( server/Main.hs, dist/build/scotty-webapp-example/scotty-webapp-example-tmp/Main.o )
Linking dist/build/scotty-webapp-example/scotty-webapp-example …
Running scotty-webapp-example…
Setting phasers to stun… (port 3000) (ctrl-c to quit) 
```

服务器现在将在端口 3000 上运行。让我们验证一下(使用优秀的 [http](https://github.com/jkbrzt/httpie) 工具:

```
% http delete :3000
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Date: Mon, 14 Sep 2015 05:44:57 GMT
Server: Warp/3.1.3
Transfer-Encoding: chunked
This was a DELETE request! 
```

太好了！

## 处理更复杂的请求

让我们再添加几个处理程序来处理不同类型的请求。将以下内容添加到`server/Main.hs` :

```
-- set a header:
post "/set-headers" $ do
 status status302  -- Respond with HTTP 302 status code
 setHeader "Location" "http://www.google.com.au"
-- named parameters:
get "/askfor/:word" $ do
  w <- param "word"
  html $ mconcat ["<h1>You asked for ", w, ", you got it!</h1>" ]
-- unnamed parameters from a query string or a form:
post "/submit" $ do  -- e.g. http://server.com/submit?name=somename
 name <- param "name"
 text name
-- match a route regardless of the method
matchAny "/all" $ do
 text "matches all methods"
-- handler for when there is no matched route
-- (this should be the last handler because it matches all routes)
notFound $ do
 text "there is no such route." 
```

## 编码/解码 JSON

如今，大多数 web 服务都通过 JSON 进行交互。Haskell 使用  库提供了一种类型安全的方式来编码/解码 JSON 字符串。

### 定义模型

例如，让我们创建一个可以表示新闻文章的`Article`数据类型。一篇文章由 3 个字段组成:一个整数 id，一个文本标题和一个正文文本。通过使 Article 成为`FromJSON`和`ToJSON` typeclasses 的实例，我们可以使用 Aeson 库在 JSON 字符串和 Article 对象之间进行转换。将以下代码添加到文件`server/Article.hs` :

```
{-# LANGUAGE OverloadedStrings #-}
module Article where
import Data.Text.Lazy
import Data.Text.Lazy.Encoding
import Data.Aeson
import Control.Applicative

-- Define the Article constructor
-- e.g. Article 12 "some title" "some body text"
data Article = Article Integer Text Text -- id title bodyText
     deriving (Show)

-- Tell Aeson how to create an Article object from JSON string.
instance FromJSON Article where
     parseJSON (Object v) = Article <$>
                            v .:? "id" .!= 0 <*> -- the field "id" is optional
                            v .:  "title"    <*>
                            v .:  "bodyText"

-- Tell Aeson how to convert an Article object to a JSON string.
instance ToJSON Article where
     toJSON (Article id title bodyText) =
         object ["id" .= id,
                 "title" .= title,
                 "bodyText" .= bodyText] 
```

我们需要给 Scotty router 函数添加几个路由来处理编码和解码文章类型:

```
main = scotty 3000 $ do

  -- get article (json)
  get "/article" $ do
    json $ Article 13 "caption" "content" -- Call Article constructor and encode the result as JSON

  -- post article (json)
  post "/article" $ do
    article <- jsonData :: ActionM Article -- Decode body of the POST request as an Article object
    json article                           -- Send the encoded object back as JSON 
```

我们还需要向`scotty-webapp-example.cabal`文件添加几个依赖项:

```
build-depends: base >=4.8 && <4.9
             , scotty
             , http-types
             , text
             , aeson 
```

### 测试 JSON 编码/解码

让我们启动 Scotty，看看它能否正确处理 JSON:

#### 获取/文章

```
% http get :3000/article
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Mon, 14 Sep 2015 06:51:17 GMT
Server: Warp/3.1.3
Transfer-Encoding: chunked
{
  “bodyText”: “content”,
  “id”: 13,
  “title”: “caption”
} 
```

#### 帖子/文章

```
% http post :3000/article id:=23 title=”new caption” bodyText=”some content”
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Mon, 14 Sep 2015 06:56:57 GMT
Server: Warp/3.1.3
Transfer-Encoding: chunked
{
  “bodyText”: “some content”,
  “id”: 23,
  “title”: “new caption”
} 
```

## 来源

本节的完整源代码可在 [github](https://github.com/parambirs/hello-scotty/tree/with-json-types) 上获得

# 终于

我希望这个教程对你有用。如果有些东西对你不起作用，或者我错过了记录任何步骤，请让我知道。