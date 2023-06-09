# 在 Python 中使用 JSON

> 原文：<https://dev.to/twilio/working-with-json-in-python-4hd1>

开发人员经常需要处理各种不同格式的数据，JSON 是 JavaScript Object Notation 的缩写，是 web 开发中最流行的格式之一。这是 JavaScript 语言用来表示对象的语法。

作为一名 Python 开发人员，您可能会注意到这看起来与 Python 字典极其相似。在 Python 中使用 JSON 有几种不同的解决方案，并且这些数据通常被加载到一个字典中。

在这篇文章中，我们将使用来自 [NASA 的每日天文图片 API](https://api.nasa.gov/api.html#apod) 的以下修改的 JSON 数据。导航到想要运行示例代码的位置，创建一个名为`apod.json`的文件，并向其中添加以下内容:

```
{  "copyright":  "Yin Hao",  "date":  "2018-10-30",  "explanation":  "Meteors have been shooting out from the constellation of Orion...",  "hdurl":  "https://apod.nasa.gov/apod/image/1810/Orionids_Hao_2324.jpg",  "media_type":  "image",  "service_version":  "v1",  "title":  "Orionids Meteors over Inner Mongolia",  "url":  "https://apod.nasa.gov/apod/image/1810/Orionids_Hao_960.jpg"  } 
```

使用这个例子，让我们看看如何用不同的 Python 库解码和编码这些数据。

### 标准库

让我们从最明显的选择开始，Python 标准库中的[原生 JSON 模块](https://docs.python.org/3/library/json.html)。这个库以一种相当容易使用的方式完成了编码和解码 JSON 的任务。许多其他 JSON 库的 API 都基于这个库，并且行为类似。

创建一个名为`test.py`的文件，并将以下代码粘贴到其中，以解码我们的`apod.json`文本文件中的 JSON，将其存储在 Python 字典中，然后解码回字符串:

```
import json

with open('apod.json', 'r') as f:
        json_text = f.read()

# Decode the JSON string into a Python dictionary. apod_dict = json.loads(json_text)
print(apod_dict['explanation'])

# Encode the Python dictionary into a JSON string. new_json_string = json.dumps(apod_dict, indent=4)
print(new_json_string) 
```

使用以下命令运行您的代码:

```
python test.py 
```

使用内置 JSON 模块的一个好处是，您不必安装任何第三方库，允许您拥有最少的依赖性。

### 简单 json

simplejson 是一个简单快速的 json 库，功能类似于内置模块。simplejson 很酷的一点是，它是外部维护的，并且定期更新。

您将不得不使用 pip 安装此模块。因此，在您的终端中，运行以下命令([最好是在虚拟环境中](https://www.twilio.com/docs/usage/quickstart/devenvironment-python) ):

```
pip install simplejson==3.16.0 
```

该库的设计与内置模块非常相似，因此您甚至不必更改代码就可以获得相同的功能！只需导入`simplejson`模块，将其命名为`json`，上一个示例中的其余代码应该就可以工作了。

如果您想使用 simplejson 进行编码和解码，请用下面的代码替换之前的代码:

```
import simplejson as json

with open('apod.json', 'r') as f:
        json_text = f.read()

# Decode the JSON string into a Python dictionary. apod_dict = json.loads(json_text)
print(apod_dict['explanation'])

# Encode the Python dictionary into a JSON string. new_json_string = json.dumps(apod_dict, indent=4)
print(new_json_string) 
```

同样，使用下面的命令运行它:

```
python test.py 
```

许多 Python 开发人员会建议在大多数情况下使用 simplejson 来代替普通的 json 库，因为它维护得很好。

### UltraJSON

像 simplejson 一样， [ujson](https://pypi.org/project/ujson/) 是另一个社区维护的 json 库。然而，这个是用 C 写的，被设计成[真的很快](https://artem.krylysov.com/blog/2015/09/29/benchmark-python-json-libraries/)。它缺少内置 JSON 库所具有的一些更高级的特性，但确实实现了它的承诺，因为它在速度方面似乎是无与伦比的。

使用以下命令安装 ujson:

```
pip install ujson==1.35 
```

与 simplejson 一样，您不需要修改任何代码就可以让它工作。在大多数情况下，从开发人员的角度来看，它的工作方式与内置模块相同。用下面的代码替换之前的代码:

```
import ujson as json

with open('apod.json', 'r') as f:
        json_text = f.read()

# Decode the JSON string into a Python dictionary. apod_dict = json.loads(json_text)
print(apod_dict['explanation'])

# Encode the Python dictionary into a JSON string. new_json_string = json.dumps(apod_dict, indent=4)
print(new_json_string) 
```

使用以下命令运行此命令:

```
python test.py 
```

如果您正在处理非常大的数据集，并且 JSON 序列化正在成为一项昂贵的任务，那么 ujson 是一个很好的库。

### 请求库

这些 JSON 序列化库很棒，但是在现实世界中，通常会有更多的关于为什么必须处理 JSON 数据的上下文。需要解码 JSON 的最常见场景之一是向第三方 REST APIs 发出 HTTP 请求。

[requests](http://docs.python-requests.org/en/master/) 库是最流行的生成 HTTP 请求的 Python 工具，它有一个非常棒的内置于响应对象上的`json()`方法，当你的 HTTP 请求完成时返回该方法。有一个内置的解决方案是很棒的，这样你就不必为一个简单的任务导入更多的库。

使用以下 shell 命令安装请求:

```
pip install requests==2.20.0 
```

在这个例子中，我们实际上要向天文图片 API 发出一个 HTTP 请求，而不是使用其他例子中的本地硬编码的`.json`文件。

打开一个名为`apod.py`的新文件，并在其中添加以下代码:

```
import requests

apod_url = 'https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY'
apod_dict = requests.get(apod_url).json()

print(apod_dict['explanation']) 
```

这段代码向 NASA 的 API 发出一个 HTTP `GET`请求，使用这个内置方法解析它返回的 JSON 数据，并打印出当天当前天文学图片的解释。

使用以下命令运行您的代码:

```
python apod.py 
```

### 用 Flask 中的 JSON 响应 HTTP 请求

另一个常见的场景是，您在 web 应用程序上构建一个路由，并希望用 JSON 数据响应请求。Python 的一个流行的轻量级 web 框架 Flask ，有一个内置的 [jsonify](http://flask.pocoo.org/docs/1.0/api/#flask.json.jsonify) 函数来为你处理序列化数据。

用 pip 安装烧瓶:

```
pip install flask==1.0.2 
```

现在创建一个名为`app.py`的新文件，我们的示例 web 应用程序的代码将保存在这个文件中:

```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/apod', methods=['GET'])
def apod():
        url = 'https://apod.nasa.gov/apod/image/1810/Orionids_Hao_960.jpg'
        title = 'Orionids Meteors over Inner Mongolia'

        return jsonify(url=url, title=title)

if __name__ == '__main__':
        app.run() 
```

在这段代码中，我们有一个名为`/apod`的路由，每当一个`GET`请求被发送到该路由时，就会调用`apod()`函数。在这个函数中，我们假装用当天的天文图片来响应。在这个例子中，我们返回的数据只是硬编码的，但是您可以用任何其他来源的数据来替换它。

用`python app.py`运行文件，然后在浏览器中访问[http://localhost:5000/apod](http://localhost:5000/apod)查看 JSON 数据。

根据 Flask 文档，`jsonify`函数以下列形式获取数据:

1.  单个参数:直接传递给 [dumps()](http://flask.pocoo.org/docs/1.0/api/#flask.json.dumps) 。
2.  多个参数:在传递给 [dumps()](http://flask.pocoo.org/docs/1.0/api/#flask.json.dumps) 之前转换为一个数组。
3.  多个关键字参数:在传递给 [dumps()](http://flask.pocoo.org/docs/1.0/api/#flask.json.dumps) 之前转换成一个 dict。
4.  args 和 kwargs:行为未定义，将抛出异常。

这个函数包装了 [dumps()](http://flask.pocoo.org/docs/1.0/api/#flask.json.dumps) 以增加一些增强功能，使生活变得更加轻松。它将 JSON 输出转换成一个带有 application/json mimetype 的[响应](http://flask.pocoo.org/docs/1.0/api/#flask.Response)对象。

### 结论

在 Python 中使用 JSON 有许多不同的解决方案，在本文中我只展示了几个例子。您可以使用任何适合您个人需要的库，或者在 requests 和 Flask 的情况下，甚至不必导入特定的 JSON 库。

如有任何问题或展示您的成果，请随时联系我们:

*   Email: [Sagnew@twilio.com](mailto:Sagnew@twilio.com)
*   推特:[@ sagnewthreds](http://twitter.com/sagnewshreds)
*   GitHub: [Sagnew](https://github.com/sagnew)
*   Twitch(流直播代码):[sagnewthreads](http://www.twitch.tv/sagnewshreds)