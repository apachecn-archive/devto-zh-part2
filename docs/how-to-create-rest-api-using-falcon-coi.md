# 如何使用 Falcon 创建 REST API

> 原文：<https://dev.to/apcelent/how-to-create-rest-api-using-falcon-coi>

[Falcon](https://falconframework.org/) 是一个裸机 [Python](https://www.python.org/) web API 框架，用于构建快速 app 后端和微服务。裸机服务器是完全专用于单个客户的单租户物理服务器。

根据猎鹰官方网站

`“When it comes to building HTTP APIs, other frameworks weigh you down with tons of dependencies and unnecessary abstractions. Falcon cuts to the chase with a clean design that embraces HTTP and the REST architectural style.”`

[![Alt text of image](img/0480adc1cafad42684adb8da58cbd79c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aPN6VfcH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/rest_api_falcon.png)

在本文中，我们将使用 falcon 为我们的应用程序创建一个 rest API。该应用程序将执行以下任务:

*   创建注释(发布请求)
*   获取注释(获取请求)
*   编辑注释(上传请求)
*   删除注释(删除请求)

我们将使用 SQLAlchemy 作为 API 的资源提供者，并使用 gunicorn 来服务这些 API。

## 架起猎鹰

使用 python3 建立并激活虚拟环境。安装猎鹰、SQLAlchemy 和 gunicorn

```
pip install falcon
pip install SQLAlchemy
pip install gunicorn 
```

Enter fullscreen mode Exit fullscreen mode

或者克隆 [github 库](https://github.com/vibhash1083/falcon-note)并安装需求

```
pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 型号

一旦包安装完成，我们将在`models.py`文件中设置 SQLAlchemy 作为我们的资源提供者:

```
if __name__ == "__main__":
    from sqlalchemy import create_engine

    engine = create_engine(DB_URI)
    Base.metadata.drop_all(engine)
    Base.metadata.create_all(engine) 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们在`models.py`
中添加一个用于创建音符对象的音符模型类

```
class Note(Base):
    __tablename__ = 'notes'
    id      = Column(Integer, primary_key=True)
    title    = Column(String(50))
    description     = Column(String(50))
    created_at     = Column(String(50))
    created_by     = Column(String(50))
    priority     = Column(Integer) 
```

Enter fullscreen mode Exit fullscreen mode

在`resources.py`文件中，我们基于笔记模型定义笔记资源。

```
class NoteCollectionResource(CollectionResource):
    model = Note
    methods = ['GET', 'POST']

class NoteResource(SingleResource):
    model = Note 
```

Enter fullscreen mode Exit fullscreen mode

定义模型和资源后，我们在`app.py`中添加 API urls。

```
db_engine = create_engine('sqlite:///stuff.db')

app = falcon.API(
    middleware=[Middleware()],
)

app.add_route('/notes', NoteCollectionResource(db_engine))
app.add_route('/notes/{id}', NoteResource(db_engine)) 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 app

通过在终端
中执行以下命令创建数据库

```
python models.py 
```

Enter fullscreen mode Exit fullscreen mode

运行服务器

```
gunicorn --reload app:app 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在[http://127 . 0 . 0 . 1:8000/notes](http://127.0.0.1:8000/notes)查看所有请求的列表。

## 使用 API 进行注释

打开 python shell 并执行以下请求:

```
requests.post('http://localhost:8000/notes/',
                 headers={'Content-Type': 'application/json'},
                 data=json.dumps({'title': 'sample note five' , 'description': 'sample notes',
                                    'created_at': '2017-08-18 00:00:00', 'created_by': 'apcelent',
                                    'priority': 3}))

requests.put('http://localhost:8000/notes/2',
                headers={'Content-Type': 'application/json'},
                data=json.dumps({'title': 'sample note five' , 'description': 'sample notes edit',
                                    'created_at': '2017-08-18 00:00:00', 'created_by': 'apcelent',
                                    'priority': 3}))

requests.delete('http://localhost:8000/notes/2') 
```

Enter fullscreen mode Exit fullscreen mode

## 表现

官方网站上 Falcon 的基准测试结果显示，Falcon 的性能优于其他一些流行的 python 框架，能够在更短的时间内处理更大的请求，使其快速可靠地用于大规模微服务和后端。

源代码可以在 [github](https://github.com/codecraf8/rest-api-python-falcon) 上找到

希望这篇文章有所帮助！

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/create-rest-api-using-falcon.html)上。