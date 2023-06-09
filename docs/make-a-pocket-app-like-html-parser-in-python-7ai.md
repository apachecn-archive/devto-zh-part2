# 用 Python 做一个类似 html 解析器的口袋 app

> 原文：<https://dev.to/brijesh/make-a-pocket-app-like-html-parser-in-python-7ai>

本教程将展示如何通过 URL 从任何文章或博客文章中提取相关的 html。

我们大多数人都在手机或浏览器上使用了 Pocket app 来保存链接，供日后阅读。它有点像一个书签应用程序，但也可以保存链接的内容。将链接添加到 Pocket 后，您可以看到它仅提取文章的主要内容，而丢弃网站的脚注、菜单、边栏(如果有)、用户注释等其他内容。我们不会进入与识别具有最多文本内容的 html 标记相关的算法。你可以在 [stackoverflow](http://stackoverflow.com/questions/3652657/what-algorithm-does-readability-use-for-extracting-text-from-urls) 上读到关于算法的讨论。

### 报纸

报纸是一个 Python 模块，处理从 URL 中提取文本/html。除此之外，它还可以提取文章的标题，作者，出版时间，图像，视频等。如果与`nltk`结合使用，它还可以提取文章的关键词和摘要。

在本教程中，我们将使用[报纸](https://github.com/codelucas/newspaper)提取包含 URL 相关内容的 html 标签，然后我们将使用 [flask](http://flask.pocoo.org/) 在 web 上公开该功能。

## 开始使用

为了简化教程，我们将在全局 Python 环境中工作。但是您应该在虚拟环境中完成下面描述的所有过程。

1.  使用`pip`安装`newspaper`和`flask`。

    *   对于`python2.x`、`pip install newspaper flask`
    *   对于`python3.x`、`pip install newspaper3k flask`
    *   如果在安装`newspaper`时有一些错误，你可以在这里阅读针对你的平台[的详细指南。](https://pypi.python.org/pypi/newspaper#get-it-now)
2.  用下面的代码创建一个名为`extractor.py`的文件。

```
from newspaper import Article, Config

config = Config()
config.keep_article_html = True

def extract(url):
    article = Article(url=url, config=config)
    article.download()
    article.parse()
    return dict(
        title=article.title,
        text=article.text,
        html=article.html,
        image=article.top_image,
        authors=article.authors,
    ) 
```

Enter fullscreen mode Exit fullscreen mode

由于`newspaper`完成了所有繁重的工作，这是我们主要部分需要的唯一代码。它识别包含最相关数据的 html 元素，清理它，删除任何`script`、`style`和其他不相关的标签，这些标签不太可能构成主文章。

在上面的代码中，`Article`是`newspaper`从 URL 中表示文章的方式。默认情况下，`newspaper`并不保存文章内容的 html 来保存一些额外的处理。这就是为什么我们从`newspaper`导入`Config`，并创建一个自定义配置，告诉`newspaper`保持文章的 html 格式。

*   在接受一个`url`的`extract`函数中，我们首先创建一个传入`url`和自定义`config`的`Article`实例。
*   然后我们用`article.download()`下载整篇文章 html。`newspaper`仍然没有处理完整的 html。
*   现在我们叫`article.parse()`。在此之后，将生成与`url`中的文章相关的所有必要数据。这些数据包括文章的标题、全文、作者、图片等。
*   然后我们在一个`dict`中返回我们需要的数据。

### 作为 API 公开

既然我们已经创建了提取文章的功能，我们将把它放到网上，这样我们就可以在我们的浏览器中测试它。我们将使用`flask`来制作一个 API。这是代码。

```
from flask import (
    Flask,
    jsonify,
    request
)

from extractor import extract

app = Flask(__name__)

@app.route('/')
def index():
    return """
    <form action="/extract">
        <input type="text" name="url" placeholder="Enter a URL" />
        <button type="submit">Submit</button>
    </form>
    """

@app.route('/extract')
def extract_url():
    url = request.args.get('url', '')
    if not url:
        return jsonify(
            type='error', result='Provide a URL'), 406
    return jsonify(type='success', result=extract(url))

 if __name__ == '__main__':
     app.run(debug=True, port=5000) 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们首先创建一个`Flask`应用程序。
2.  在它的`index`路线中，我们返回一个带有文本输入的简单表单，用户将粘贴 url，然后将表单提交给在`action`中指定的`/extract`路线。
3.  在`extract_url`函数中，我们从`request.args`获取`url`并检查它是否为空。如果为空，我们返回一个错误。否则将把`url`传递给我们创建的`extract`函数，然后使用`jsonify`返回结果。
4.  现在，您可以简单地运行`python app.py`并在浏览器中进入 [http://localhost:5000](http://localhost:5000) 来测试实现。

[完整代码](https://github.com/brijeshb42/extractor)

#### 很少要记住的事情

*   除了检查 url 中的空字符串之外，我们还可以使用 regex 来验证该 url 实际上是一个 url。
*   我们还应该检查一下`url`不是我们自己的域，因为它会导致一个无限循环，反复调用同一个`extract_url`函数。
*   `newspaper`并不总是能够提取最相关的 html。它的功能完全取决于内容在源`url`的网站中是如何组织的。有时，它可能会给出一个或两个额外的段落，有时甚至更少。但是对于大多数标准的新闻网站和博客，它总是会返回最相关的 html。

#### 接下来要做的事情

上面的演示是一个非常简单的应用程序，它接收一个 URL，返回 html，然后忘记它。但是，为了使它更有用，您可以通过以下方式更进一步:

1.  添加数据库
2.  保存 url 及其提取的内容，以便在再次提供相同的 URL 时可以从 DB 返回结果。
3.  您可以添加一些更高级的 API，比如返回最近查询/保存的 URL 及其标题和其他内容的列表。
4.  然后，您可以使用 API 服务创建一个 web 或 android/ios 应用程序，其功能类似于 [Pocket](https://getpocket.com/) 的功能。

#### 阅读更多

*   [报纸 API](http://newspaper.readthedocs.io/en/latest/) 。

这篇文章最初发表在的 [bitwiser.in](http://bitwiser.in/2016/06/09/make-a-pocket-app-like-html-parser-using-python.html)