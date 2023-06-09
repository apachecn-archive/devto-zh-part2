# 如何使用 aiohttp 创建 REST API

> 原文：<https://dev.to/apcelent/how-to-create-rest-api-using-aiohttp-54p1>

[aiohttp](https://aiohttp.readthedocs.io/en/stable/) 是针对 [python](https://www.python.org/) 和 [AsyncIO](https://docs.python.org/3/library/asyncio.html) 的 http 客户端/服务器。它支持服务器 websockets 和客户端 websockets。由于它是异步工作的，它每秒可以处理数百个请求，提供了比其他框架更好的性能。

[AsyncIO](https://docs.python.org/3/library/asyncio.html) 是一个用于编写的 [python](https://www.python.org/) 库:

*   使用协程的单线程并发代码。

*   通过套接字和其他资源多路复用 I/O 访问。

*   运行网络客户端和服务器，以及其他相关的原语。

这尤其为套接字和其他资源上的 I/O 绑定任务提供了并发性。并发性确保用户不必等待 I/O 绑定的结果。

[![Alt text of image](img/eb03012c273da77413d6437133c3867b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IwGqXidK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/rest_api_using_aiohttp.png)

在本文中，我们将使用 aiohttp 为我们的应用程序创建一个 rest API。这是一个简单的应用程序，有一个注释表。

## 设置 aiohttp

在 python 3 中激活一个虚拟环境，安装 aiohttp

```
pip install aiohttp 
```

Enter fullscreen mode Exit fullscreen mode

或者克隆 github 库并安装需求

```
pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 创建模型

我们将在`models.py`
中配置应用程序使用 sqlite 作为我们的数据库

```
# DBSession = scoped_session(sessionmaker(extension=ZopeTransactionExtension())) DB_URI = 'sqlite:///stuff.db'

Session = sessionmaker(autocommit=False,
                       autoflush=False,
                       bind=create_engine(DB_URI))
session = scoped_session(Session)
Base = declarative_base() 
```

Enter fullscreen mode Exit fullscreen mode

然后我们为`models.py`
中的音符对象创建音符类

```
class Note(Base):

    __tablename__ = 'notes'

    id      = Column(Integer, primary_key=True)
    title    = Column(String(50))
    description     = Column(String(50))
    created_at     = Column(String(50))
    created_by     = Column(String(50))
    priority     = Column(Integer)

    def __init__(self, title, description, created_at ,created_by, priority):

        self.title = title
        self.description = description
        self.created_at = created_at
        self.created_by = created_by
        self.priority = priority

    @classmethod
    def from_json(cls, data):
        return cls(**data)

    def to_json(self):
        to_serialize = ['id', 'title', 'description', 'created_at', 'created_by', 'priority']
        d = {}
        for attr_name in to_serialize:
            d[attr_name] = getattr(self, attr_name)
        return d 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

我们在`aiohttp_rest.py`文件中定义我们的 API 端点。

```
DEFAULT_METHODS = ('GET', 'POST', 'PUT', 'DELETE')

class RestEndpoint:

    def __init__(self):
        self.methods = {}

        for method_name in DEFAULT_METHODS:
            method = getattr(self, method_name.lower(), None)
            if method:
                self.register_method(method_name, method)

    def register_method(self, method_name, method):
        self.methods[method_name.upper()] = method

    async def dispatch(self, request: Request):
        method = self.methods.get(request.method.upper())
        if not method:
            raise HTTPMethodNotAllowed('', DEFAULT_METHODS)

        wanted_args = list(inspect.signature(method).parameters.keys())
        available_args = request.match_info.copy()
        available_args.update({'request': request})

        unsatisfied_args = set(wanted_args) - set(available_args.keys())
        if unsatisfied_args:
            # Expected match info that doesn't exist
            raise HttpBadRequest('')

        return await method(**{arg_name: available_args[arg_name] for arg_name in wanted_args})

class CollectionEndpoint(RestEndpoint):
    def __init__(self, resource):
        super().__init__()
        self.resource = resource

    async def get(self) -> Response:
        data = []

        notes = session.query(Note).all()
        for instance in self.resource.collection.values():
            data.append(self.resource.render(instance))
        data = self.resource.encode(data)
        return Response ( status=200, body=self.resource.encode({
            'notes': [
                {'id': note.id, 'title': note.title, 'description': note.description,
                'created_at': note.created_at, 'created_by': note.created_by, 'priority': note.priority}

                    for note in session.query(Note)

                    ]
            }), content_type='application/json')

    async def post(self, request):
        data = await request.json()
            note=Note(title=data['title'], description=data['description'], created_at=data['created_at'], created_by=data['created_by'], priority=data['priority'])
        session.add(note)
        session.commit()

        return Response(status=201, body=self.resource.encode({
            'notes': [
                {'id': note.id, 'title': note.title, 'description': note.description,
                'created_at': note.created_at, 'created_by': note.created_by, 'priority': note.priority}

                    for note in session.query(Note)

                    ]
            }), content_type='application/json')

class InstanceEndpoint(RestEndpoint):
    def __init__(self, resource):
        super().__init__()
        self.resource = resource

    async def get(self, instance_id):
        instance = session.query(Note).filter(Note.id == instance_id).first()
        if not instance:
            return Response(status=404, body=json.dumps({'not found': 404}), content_type='application/json')
        data = self.resource.render_and_encode(instance)
        return Response(status=200, body=data, content_type='application/json')

    async def put(self, request, instance_id):

        data = await request.json()

        note = session.query(Note).filter(Note.id == instance_id).first()
        note.title = data['title']
        note.description = data['description']
        note.created_at = data['created_at']
        note.created_by = data['created_by']
        note.priority = data['priority']
        session.add(note)
        session.commit()

        return Response(status=201, body=self.resource.render_and_encode(note),
                        content_type='application/json')

    async def delete(self, instance_id):
        note = session.query(Note).filter(Note.id == instance_id).first()
        if not note:
            abort(404, message="Note {} doesn't exist".format(id))
        session.delete(note)
        session.commit()
        return Response(status=204)

class RestResource:
    def __init__(self, notes, factory, collection, properties, id_field):
        self.notes = notes
        self.factory = factory
        self.collection = collection
        self.properties = properties
        self.id_field = id_field

        self.collection_endpoint = CollectionEndpoint(self)
        self.instance_endpoint = InstanceEndpoint(self)

    def register(self, router: UrlDispatcher):
        router.add_route('*', '/{notes}'.format(notes=self.notes), self.collection_endpoint.dispatch)
        router.add_route('*', '/{notes}/{{instance_id}}'.format(notes=self.notes), self.instance_endpoint.dispatch)

    def render(self, instance):
        return OrderedDict((notes, getattr(instance, notes)) for notes in self.properties)

    @staticmethod
    def encode(data):
        return json.dumps(data, indent=4).encode('utf-8')

    def render_and_encode(self, instance):
        return self.encode(self.render(instance)) 
```

Enter fullscreen mode Exit fullscreen mode

通过对所有方法(GET、POST、PUT 和 DELETE)使用 async 关键字，我们确保这些操作异步执行，并且从集合端点和实例端点返回响应。设置完端点后，我们在`aio-app.py`文件中声明资源。

```
from aiohttp.web import Application, run_app

from aiohttp_rest import RestResource
from models import Note
from sqlalchemy import engine_from_config

notes = {}
app = Application()
person_resource = RestResource('notes', Note, notes, ('title', 'description', 'created_at', 'created_by', 'priority'), 'title')
person_resource.register(app.router)

if __name__ == '__main__':

    run_app(app) 
```

Enter fullscreen mode Exit fullscreen mode

## 运行应用程序

首先通过
创建数据库

```
python models.py 
```

Enter fullscreen mode Exit fullscreen mode

通过在终端
中执行以下操作来运行应用程序

```
python aio-app.py 
```

Enter fullscreen mode Exit fullscreen mode

打开 python shell 并执行一些请求

```
requests.post('http://localhost:8080/notes',
                 data=json.dumps({ "title": "note two",
                 "created_at": "2017-08-23 00:00", "created_by": "apcelent", "description": "sample notes", "priority": 4
}))

requests.put('http://localhost:8080/notes/1',
                 data=json.dumps({ "title": "note edit",
                 "created_at": "2017-08-23 00:00", "created_by": "apcelent", "description": "sample notes edit", "priority": 4
}))

requests.delete('http://localhost:8080/notes/1') 
```

Enter fullscreen mode Exit fullscreen mode

这些将使用 aiohttp REST API 在数据库中创建一些注释。这些笔记可以在[http://127 . 0 . 0 . 1:8080/notes](http://127.0.0.1:8080/notes)上查看

源代码可以在[这里](https://github.com/codecraf8/python-rest-api-aiohttp)找到。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/create-rest-api-using-aiohttp.html)上。