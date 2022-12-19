# 使用 Python 和 Flask 进行 Elasticsearch

> 原文：<https://dev.to/itachiuchiha/using-elasticsearch-with-python-and-flask-2i0e>

[![https://thepracticaldev.s3.amazonaws.com/i/de6c7211jvv32ipbvfvm.png](img/7ec5fad9d12c2a234c15c7e01f786d07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gWCNv_HR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/de6c7211jvv32ipbvfvm.png)

[这篇文章最初是在我的土耳其博客上发表的](https://aligoren.com/building-basic-restful-api-with-flask/)

首先，我为我的语法错误感到抱歉。

嗨，在这篇文章中，我将给出一些关于使用 Python 和 Elasticsearch 的信息。

# 什么是 Elasticsearch？

Elasticsearch 是一个基于 Apache Lucene 的开源、RESTful、分布式搜索和分析引擎。([通过](https://aws.amazon.com/elasticsearch-service/what-is-elasticsearch/))

# 使用 Python 和 Flask 进行 Elasticsearch

在我开始这篇文章之前，我应该这样说；我将使用 Flask 框架。我们将通过邮递员发送我们的询问。

但是您已经在现实应用程序中的不同场景中设计了它。

# 要求

必须安装 JDK。在这篇文章中，我不会分享任何关于如何安装 JDK / JRE 的信息。关于 JDK 的安装有不同的文件。这些取决于您的操作系统。

如果您的系统上没有 JDK，您应该安装它。

# 安装

在这篇文章中，我将给出一些关于 elastic search 6 . 2 . 1 版本的信息。

官方网站的下载链接包括基于 Debian 的版本、Fedora / RedHat 的 RPM 和类似版本，以及 Windows 的 MSI。ZIP / TAR 存档可供其他人使用。

**下载链接:**[https://www . elastic . co/downloads/past-releases/elastic search-6-2-1](https://www.elastic.co/downloads/past-releases/elasticsearch-6-2-1)

如果你用的是 Ubuntu，安装会是这样:

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.1.deb

sudo dpkg -i elasticsearch-6.2.1.deb 
```

Enter fullscreen mode Exit fullscreen mode

因为我们安装了 Java 依赖项，所以不会出现任何错误。之后，开始弹性服务:

```
sudo systemctl enable elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

我们不会做最大的配置，因为这是基本级别。但这将是我们学习如何进行一些基本配置的良好开端。

配置文件是 **elasticsearch.yml** 。我们将编辑这个文件。

```
sudo nano /etc/elasticsearch/elasticsearch.yml 
```

Enter fullscreen mode Exit fullscreen mode

让我们用 CTRL + W 找到 **cluster.name** 并更新它的值。我们组合的名字叫埃尔顿·约翰。当然，如果我们想的话。

```
cluster.name: eltonjohn
node.name: "Our  First  Node" 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要保存这个文件。为了保存文件，我们将使用这些键盘组合；

```
CTRL + X
Y
Enter 
```

Enter fullscreen mode Exit fullscreen mode

需要重新启动 Elasticsearch 服务。

```
sudo systemctl restart elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

让我们从控制台屏幕测试下面的 curl 请求:

```
curl -X GET 'http://localhost:9200' 
```

Enter fullscreen mode Exit fullscreen mode

Elasticsearch 目前正在 9200 端口上工作。但是您可以设置自己的端口号。上述请求返回了以下结果。

```
{  "name":  "Our First Node",  "cluster_name":  "eltonjohn",  "cluster_uuid":  "FArhKpkhSZqjDfs3oSBMHw",  "version":  {  "number":  "6.2.1",  "build_hash":  "7299dc3",  "build_date":  "2018-02-07T19:34:26.990113Z",  "build_snapshot":  false,  "lucene_version":  "7.2.1",  "minimum_wire_compatibility_version":  "5.6.0",  "minimum_index_compatibility_version":  "5.0.0"  },  "tagline":  "You Know, for Search"  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，请求 Elasticsearch 的端点不应该可以从每个人都可以访问的网络活动屏幕中追踪到。

出于这个原因，让我们用 Python 和 Flask 这样做，如下所示:

# 用 Virtualenv 搜索安装烧瓶和橡皮筋

首先，让我们用 Virtualenv:
进行烧瓶设置

```
virtualenv venv

. venv/bin/activate

pip install Flask 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将设置 elasticsearch-py 模块:

```
pip install elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

更多信息:[https://elasticsearch-py.readthedocs.io/en/master/](https://elasticsearch-py.readthedocs.io/en/master/)

为 Python 安装了依赖项。让我们创建一个名为 main.py 的文件，并导入 elasticsearch-py 模块和 Flask。

```
from datetime import datetime
from flask import Flask, jsonify, request
from elasticsearch import Elasticsearch 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们初始化 Flask 和 Elasticsearch 模块。

```
es = Elasticsearch()

app = Flask(__name__) 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建三种方法。正常情况下，我们不应该这样做。但我说的只是 Elasticsearch。

```
@app.route('/', methods=['GET'])
def index():
    results = es.get(index='contents', doc_type='title', id='my-new-slug')
    return jsonify(results['_source']) 
```

Enter fullscreen mode Exit fullscreen mode

第一种方法将有助于从 **id** 查询到我们在 Elasticsearch 上指定的**索引**和**文档**类型。由于此时没有内容，因此将返回空结果。

我们应该知道这一点；**索引**、 **doc_type** 和 **id** 值是完全可编程的。

如果您显示 get 方法的返回值，您将需要查看 **_source** 键。

```
@app.route('/insert_data', methods=['POST'])
def insert_data():
    slug = request.form['slug']
    title = request.form['title']
    content = request.form['content']

    body = {
        'slug': slug,
        'title': title,
        'content': content,
        'timestamp': datetime.now()
    }

    result = es.index(index='contents', doc_type='title', id=slug, body=body)

    return jsonify(result) 
```

Enter fullscreen mode Exit fullscreen mode

在这一步中，假设我们在名称 slug、title 和 content 中发布了三个表单值。让我们将这些返回值传递给 index()方法的主体参数。我们将能够添加新的数据。

如果您愿意，可以创建一个索引，更改文档类型，并添加不同的辅助信息。

如果您传递的值已经被索引，则更新版本而不是添加新数据。

让我们进行最后一步。我们将在这一点上学习搜索。让我们为此创建第三个也是最后一个方法:

```
@app.route('/search', methods=['POST'])
def search():
    keyword = request.form['keyword']

    body = {
        "query": {
            "multi_match": {
                "query": keyword,
                "fields": ["content", "title"]
            }
        }
    }

    res = es.search(index="contents", doc_type="title", body=body)

    return jsonify(res['hits']['hits']) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将使用一个名为 **multi_match** 的属性。因此，我们将能够通过多个主体键进行查询。搜索文本来自一个名为**关键字**的表单值。

返回值中的第二个命中将是 JSON 数组。因此，将列出所有内容相似的值。

```
{  "_shards":  {  "failed":  0,  "skipped":  0,  "successful":  5,  "total":  5  },  "hits":  {  "hits":  [  {  "_id":  "other-my-diff",  "_index":  "contents",  "_score":  0.2876821,  "_source":  {  "content":  "What kind of content is this?",  "slug":  "other-my-diff",  "timestamp":  "2018-02-11T16:08:10.409353",  "title":  "Very different title second"  },  "_type":  "title"  },  {  "_id":  "other-my",  "_index":  "contents",  "_score":  0.2876821,  "_source":  {  "content":  "What kind of content?",  "slug":  "other-my",  "timestamp":  "2018-02-11T16:00:51.613402",  "title":  "Very different title"  },  "_type":  "title"  }  ],  "max_score":  0.2876821,  "total":  2  },  "timed_out":  false,  "took":  3  } 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。此时，elasticsearch 不应该被认为是一个数据库。

# 所有代码

所有代码都在这里。如果安装了依赖项，下面的代码将正常工作。

```
from datetime import datetime
from flask import Flask, jsonify, request
from elasticsearch import Elasticsearch

es = Elasticsearch()

app = Flask(__name__)

@app.route('/', methods=['GET'])
def index():
    results = es.get(index='contents', doc_type='title', id='my-new-slug')
    return jsonify(results['_source'])

@app.route('/insert_data', methods=['POST'])
def insert_data():
    slug = request.form['slug']
    title = request.form['title']
    content = request.form['content']

    body = {
        'slug': slug,
        'title': title,
        'content': content,
        'timestamp': datetime.now()
    }

    result = es.index(index='contents', doc_type='title', id=slug, body=body)

    return jsonify(result)

@app.route('/search', methods=['POST'])
def search():
    keyword = request.form['keyword']

    body = {
        "query": {
            "multi_match": {
                "query": keyword,
                "fields": ["content", "title"]
            }
        }
    }

    res = es.search(index="contents", doc_type="title", body=body)

    return jsonify(res['hits']['hits'])

app.run(port=5000, debug=True) 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读