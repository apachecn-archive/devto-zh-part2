# 用结构和颜色记录烧瓶请求

> 原文：<https://dev.to/rhymes/logging-flask-requests-with-colors-and-structure--7g1>

日志是所有开发者都同意的事情之一，我们不同意的是格式。

你可以使用“自由文本”记录日志，同意使用完整的 JSON 结构。

我倾向于结合使用自由文本和结构化日志。到目前为止，我最喜欢的日志库是 Rails 的 [lograge](https://github.com/roidrage/lograge) ，有趣的是作者将它描述为`"An attempt to tame Rails' default policy to log everything"`。

Flask 在默认情况下肯定没有正确记录日志，我认为这也与 Python 的默认[日志](https://docs.python.org/3/library/logging.html)有多复杂有关，老实说，我从未完全理解这一点。

总之，回到文章的范围。Lograge 非常简洁，因为它把这个:

```
Started GET "/" for 127.0.0.1 at 2012-03-10 14:28:14 +0100
Processing by HomeController#index as HTML
  Rendered text template within layouts/application (0.0ms)
  Rendered layouts/_assets.html.erb (2.0ms)
  Rendered layouts/_top.html.erb (2.6ms)
  Rendered layouts/_about.html.erb (0.3ms)
  Rendered layouts/_google_analytics.html.erb (0.4ms)
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
method=GET path=/jobs/833552.json format=json controller=JobsController  action=show status=200 duration=58.33 view=40.43 db=15.26 
```

Enter fullscreen mode Exit fullscreen mode

我想用最新发布的 Flask (v0.12.2)来复制这一点，并添加一些颜色:

[![colorized logging](img/02e1280058acbdb7ab82421b2882bc88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0YwAJShU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a810d2ap3r8fgvnncsb3.png)

**要收集的信息**

从图片中可以看出，我们需要:

*   请求方法
*   请求路径
*   响应状态代码
*   请求时间
*   RFC339 格式的时间戳
*   请求 ip
*   请求主机
*   请求参数

**如何将其收集在烧瓶中**

Flask 有钩子在请求的不同阶段注入你的代码。有点像 Rails 请求过滤器。

我们将使用请求前和请求后的[。](http://flask.pocoo.org/docs/0.12/api/#flask.Flask.before_request)

我们首先要收集它请求开始的时间戳:

```
@app.before_request
def start_timer():
    g.start = time.time() 
```

Enter fullscreen mode Exit fullscreen mode

`app`是 Flask app，`g`是 [flask 全局对象](http://flask.pocoo.org/docs/0.12/api/#flask.g)和`time.time()`井...你知道:-)

其余的信息可以在请求完成后收集:

```
@app.after_request
def log_request(response):
    if request.path == '/favicon.ico':
        return response
    elif request.path.startswith('/static'):
        return response

    now = time.time()
    duration = round(now - g.start, 2)
    dt = datetime.datetime.fromtimestamp(now)
    timestamp = rfc3339(dt, utc=True)

    ip = request.headers.get('X-Forwarded-For', request.remote_addr)
    host = request.host.split(':', 1)[0]
    args = dict(request.args) 
```

Enter fullscreen mode Exit fullscreen mode

*   这不会记录静态文件的图标或请求。你可能想留着它们
*   时间戳是用 UTC 表示的，你可能希望它在一个特定的时区，但是我倾向于用 UTC 表示日志。我使用 [rfc3339 库](https://pypi.python.org/pypi/rfc3339)
*   它从`X-Forwarded-For`中检索 IP 地址以给予代理请求优先权，默认为 Flask 的远程地址
*   检索主机时没有可选端口

下一步是创建参数并添加颜色:

```
 log_params = [
        ('method', request.method, 'blue'),
        ('path', request.path, 'blue'),
        ('status', response.status_code, 'yellow'),
        ('duration', duration, 'green'),
        ('time', timestamp, 'magenta'),
        ('ip', ip, 'red'),
        ('host', host, 'red'),
        ('params', args, 'blue')
    ]
    request_id = request.headers.get('X-Request-ID')
    if request_id:
        log_params.append(('request_id', request_id, 'yellow')) 
```

Enter fullscreen mode Exit fullscreen mode

我还可以选择记录 Heroku 设置的`request id`。

最后一部分实际上是构建行并输出:

```
 parts = []  # as any dev I hate naming temporary variables :-)
    for name, value, color in log_params:
        part = color("{}={}".format(name, value), fg=color)
        parts.append(part)
    line = " ".join(parts)

    app.logger.info(line) 
```

Enter fullscreen mode Exit fullscreen mode

`color`来自 [ansicolors](https://pypi.python.org/pypi/ansicolors) 图书馆。

这是整个片段:

```
import datetime
import time

import colors
from flask import g, request
from rfc3339 import rfc3339

app = create_your_flask_app()

@app.before_request
def start_timer():
    g.start = time.time()

@app.after_request
def log_request(response):
    if request.path == '/favicon.ico':
        return response
    elif request.path.startswith('/static'):
        return response

    now = time.time()
    duration = round(now - g.start, 2)
    dt = datetime.datetime.fromtimestamp(now)
    timestamp = rfc3339(dt, utc=True)

    ip = request.headers.get('X-Forwarded-For', request.remote_addr)
    host = request.host.split(':', 1)[0]
    args = dict(request.args)

    log_params = [
        ('method', request.method, 'blue'),
        ('path', request.path, 'blue'),
        ('status', response.status_code, 'yellow'),
        ('duration', duration, 'green'),
        ('time', timestamp, 'magenta'),
        ('ip', ip, 'red'),
        ('host', host, 'red'),
        ('params', args, 'blue')
    ]

    request_id = request.headers.get('X-Request-ID')
    if request_id:
        log_params.append(('request_id', request_id, 'yellow'))

    parts = []
    for name, value, color in log_params:
        part = colors.color("{}={}".format(name, value), fg=color)
        parts.append(part)
    line = " ".join(parts)

    app.logger.info(line)

    return response 
```

Enter fullscreen mode Exit fullscreen mode