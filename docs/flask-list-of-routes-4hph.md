# 烧瓶:路线列表

> 原文：<https://dev.to/rhymes/flask-list-of-routes-4hph>

**更新:** *从[烧瓶 1.0](http://flask.pocoo.org/docs/1.0/changelog/) 开始有一个内置的`flask routes`命令*

Rails 最酷的地方之一是`rails routes`(例如`rake routes`)，它在控制台上显示注册到应用程序的路由列表。

我有一个由不同视图和[蓝图](http://flask.pocoo.org/docs/0.12/blueprints/)组成的 Flask 应用程序，随着它变得更大更复杂，很难记住所有注册的路线。

我谷歌了一下“flask 路线列表”,结果发现了一个旧片段,它出现在最新的 Flask 版本中。

这是一个工作版本:

```
@app.cli.command()
def routes():
    'Display registered routes'
    rules = []
    for rule in app.url_map.iter_rules():
        methods = ','.join(sorted(rule.methods))
        rules.append((rule.endpoint, methods, str(rule)))

    sort_by_rule = operator.itemgetter(2)
    for endpoint, methods, rule in sorted(rules, key=sort_by_rule):
        route = '{:50s} {:25s} {}'.format(endpoint, methods, rule)
        print(route) 
```

Enter fullscreen mode Exit fullscreen mode

*   `@app.cli.command()`告诉 Flask cli 注册命令`routes`
*   行`sort_by_rule = operator.itemgetter(2)`用于创建一个函数(在下面的行中用于排序),该函数提取列表中位置 2 的项目。Python 通过这种方式对结果进行排序。

这是一个结果的例子:

```
$ flask routes
frontend.index                                     GET,HEAD,OPTIONS          /
frontend.index                                     GET,HEAD,OPTIONS          /<path:path>
admin.index                                        GET,HEAD,OPTIONS          /admin/
product.index_view                                 GET,HEAD,OPTIONS          /admin/product/
api.products_show                                  GET,HEAD,OPTIONS          /api/v1/products/<string:product_id>
rq_dashboard.overview                              GET,HEAD,OPTIONS          /jobs/
frontend.static                                    GET,HEAD,OPTIONS          /static/<path:filename> 
```

Enter fullscreen mode Exit fullscreen mode