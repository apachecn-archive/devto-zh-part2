# 如何使用 Flask 创建 REST API

> 原文：<https://dev.to/apcelent/how-to-create-rest-api-using-flask-2pki>

[Flask](http://flask.pocoo.org/) 是 [python](https://www.python.org/) 的微框架，可用于开发 web 应用。在本文中，我们将看到如何使用 [Flask-RESTful](https://flask-restful.readthedocs.io/en/latest/) 在 Flask 中创建 RESTful APIs。

[![Alt text of image](img/202ca21065188ad3cf4939abfcf27ec3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zU_nTrxf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/rest_api_flask.png)

## 安装

在建立项目之前，我们创建了一个虚拟环境来保持 python 包的安装与其他项目隔离。

为了建立一个虚拟环境，我们安装了 virtualenv

```
sudo pip install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建一个名为 venv 的虚拟环境并激活它

```
virtualenv venv
source venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们在这些虚拟环境中安装 Flask 和 Flast-RESTful

```
pip install Flask
pip install flask-restful 
```

Enter fullscreen mode Exit fullscreen mode

## 型号

我们创建一个注释模型，其中包含注释标题、描述、创建日期、创建者和优先级字段，id 作为主键。我们将使用 SQLite 作为这个应用程序的数据库。

```
from sqlalchemy import Column
from sqlalchemy import Integer
from sqlalchemy import String
from sqlalchemy.ext.declarative import declarative_base
from datetime import datetime

DB_URI = 'sqlite:///./main.db'
Base = declarative_base()

class Note(Base):
    __tablename__ = 'notes'

    id = Column(Integer, primary_key=True)
    title = Column(String(80))
    description = Column(String(80))
    create_at = Column(String(80))
    create_by = Column(String(80))
    priority = Column(Integer)

if __name__ == "__main__":
    from sqlalchemy import create_engine

    engine = create_engine(DB_URI)
    Base.metadata.drop_all(engine)
    Base.metadata.create_all(engine) 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

资源是 Flask-RESTful 的构建块。资源类用于将 HTTP 方法映射到对象。我们创建了一个`resource.py`文件，在这里我们首先定义了 NoteResource 类。

```
from models import Note
from db import session
from datetime import datetime
from flask.ext.restful import reqparse
from flask.ext.restful import abort
from flask.ext.restful import Resource
from flask.ext.restful import fields
from flask.ext.restful import marshal_with

note_fields = {
    'id': fields.Integer,
    'title': fields.String,
    'description': fields.String,
    'create_at': fields.String,
    'create_by': fields.String,
    'priority': fields.Integer
}

parser = reqparse.RequestParser()
parser.add_argument('title')
parser.add_argument('description')
parser.add_argument('create_at')
parser.add_argument('create_by')
parser.add_argument('priority')

class NoteResource(Resource):
    @marshal_with(note_fields)
    def get(self, id):
        note = session.query(Note).filter(Note.id == id).first()
        if not note:
            abort(404, message="Note {} doesn't exist".format(id))
        return note

    def delete(self, id):
        note = session.query(Note).filter(Note.id == id).first()
        if not note:
            abort(404, message="Note {} doesn't exist".format(id))
        session.delete(note)
        session.commit()
        return {}, 204

    @marshal_with(note_fields)
    def put(self, id):
        parsed_args = parser.parse_args()
        note = session.query(Note).filter(Note.id == id).first()
        note.title = parsed_args['title']
        note.description = parsed_args['description']
        note.create_at = parsed_args['create_at']
        note.create_by = parsed_args['create_by']
        note.priority = parsed_args['priority']
        session.add(note)
        session.commit()
        return note, 201 
```

Enter fullscreen mode Exit fullscreen mode

reqparse 是 Flaks-RESTful 请求解析接口，用于提供对 flask.request 对象变量的访问。

marshall_with decorator 从 API 获取数据对象并应用字段过滤。

我们为所有音符元素添加了一个额外的资源

```
class NoteListResource(Resource):
    @marshal_with(note_fields)
    def get(self):
        notes = session.query(Note).all()
        return notes

    @marshal_with(note_fields)
    def post(self):
        parsed_args = parser.parse_args()
        note = Note(title=parsed_args['title'], description=parsed_args['description'],
                    create_at=parsed_args['create_at'], create_by=parsed_args['create_by'],
                    priority=parsed_args['priority'] )
        session.add(note)
        session.commit()
        return note, 201 
```

Enter fullscreen mode Exit fullscreen mode

我们创建另一个文件`api.py`来将这些资源添加到我们的 API

```
from flask import Flask
from flask_restful import reqparse, abort, Api, Resource
from models import Note
from resources import *

app = Flask(__name__)
api = Api(app)

## Setup the API resource routing 
api.add_resource(NoteListResource, '/notes/', endpoint='notes')
api.add_resource(NoteResource, '/notes/<string:id>', endpoint='note')

if __name__ == '__main__':
    app.run(debug=True) 
```

Enter fullscreen mode Exit fullscreen mode

## 启动 Flask 服务器并发送请求

运行 flask web 服务器

```
python api.py 
```

Enter fullscreen mode Exit fullscreen mode

API 可以使用 curl 或浏览器插件进行测试，如 chrome 的 Postman 和 Firefox 的 httprequester。
要获取所有笔记的列表，请向以下 URL 发送获取请求:

[http://127 . 0 . 0 . 1:5000/notes/](http://127.0.0.1:5000/notes/)

要添加新注释，请将 POST 请求发送到相同的 url，请求的正文部分包含以下格式的数据。

```
{
    "create_at": "2017-08-17 00:00", 
    "create_by": "v", 
    "description": "sample notes from api", 
    "priority": 1, 
    "title": "sample note from api"
} 
```

Enter fullscreen mode Exit fullscreen mode

上述示例的实现和代码库可在[这里](https://github.com/codecraf8/rest-api-python-flask)获得。要设置项目，克隆存储库，在虚拟环境中安装需求并启动服务器。

希望这篇文章有所帮助！

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/create-rest-api-using-flask.html)上。