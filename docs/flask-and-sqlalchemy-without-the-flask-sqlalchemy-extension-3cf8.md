# 没有 Flask-SQLAlchemy 扩展的 Flask 和 SQLAlchemy

> 原文：<https://dev.to/nestedsoftware/flask-and-sqlalchemy-without-the-flask-sqlalchemy-extension-3cf8>

当 SQLAlchemy 与 Flask 一起使用时，标准方法是使用 Flask-SQLAlchemy 扩展。

但是，这个扩展有[一些](https://github.com/mitsuhiko/flask-sqlalchemy/pull/250#issuecomment-77504337)问题[一些](https://stackoverflow.com/questions/28789063/associate-external-class-model-with-flask-sqlalchemy)问题。特别是，我们必须为 SQLAlchemy 模型使用一个基类，这个基类创建了对 flask 的依赖(通过`flask_sqlalchemy.SQLAlchemy.db.Model`)。此外，应用程序可能不需要扩展提供的附加功能，如分页支持。

让我们看看能否找到一种方法，在 Flask 应用程序中使用普通的 SQLAlchemy，而不依赖于这个扩展。

> 本文特别关注将 Flask 应用程序直接连接到 SQLAlchemy，而不使用任何插件或扩展。它没有说明如何让 Flask 应用程序独立工作，或者 SQLAlchemy 是如何工作的。首先让这些部分分别工作可能是个好主意。

下面是设置 SQLAlchemy 会话(db.py)的代码:

```
import os

from sqlalchemy import create_engine

from sqlalchemy.orm import scoped_session
from sqlalchemy.orm import sessionmaker

engine = create_engine(os.environ['SQLALCHEMY_URL'])

Session = scoped_session(sessionmaker(bind=engine)) 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是`scoped_session`:现在当我们使用`Session`时，SQLAlchemy 将检查是否存在一个[线程本地会话](http://docs.sqlalchemy.org/en/latest/orm/contextual.html#unitofwork-contextual)。如果它已经存在，那么它将使用它，否则它将首先创建一个。

以下代码引导 Flask 应用程序(__init__)。py):

```
from flask import Flask

from .db import Session

from .hello import hello_blueprint

app = Flask(__name__)
app.register_blueprint(hello_blueprint)

@app.teardown_appcontext
def cleanup(resp_or_exc):
    Session.remove() 
```

Enter fullscreen mode Exit fullscreen mode

当当前应用程序上下文被拆除时，`@app.teardown_appcontext`装饰器将执行所提供的回调函数`cleanup`。这发生在每次请求之后。这样，我们可以确保在每次请求后释放会话使用的资源。

在我们的 Flask 应用程序中，我们现在可以使用`Session`与我们的数据库进行交互。比如(hello.py):

```
import json

from flask import Blueprint

from .db import Session

from .models import Message

hello_blueprint = Blueprint('hello', __name__)

@hello_blueprint.route('/messages')
def messages():
    values = Session.query(Message).all()

    results = [{ 'message': value.message } for value in values]

    return (json.dumps(results), 200, { 'content_type': 'application/json' }) 
```

Enter fullscreen mode Exit fullscreen mode

这应该足以将 SQLAlchemy 集成到 Flask 应用程序中。

> 有关 Flask-SQLAlchemy 提供的特性的更详细概述，请参见 Derrick Gilland 的文章[揭开 Flask-SQLAlchemy 的神秘面纱](http://derrickgilland.com/posts/demystifying-flask-sqlalchemy/)

我们还得到了不必为我们的 SQLAlchemy 模型创建对 Flask 的依赖的好处。下面我们只是使用标准的`sqlalchemy.ext.declarative.declarative_base` (models.py):

```
from sqlalchemy.ext.declarative import declarative_base

from sqlalchemy import Column, Integer, String

Base = declarative_base()

class Message(Base):
    __tablename__ = 'messages'
    id = Column(Integer, primary_key=True)
    message = Column(String)

    def __repr__(self):
        return "<Message(message='%s')>" % (self.message) 
```

Enter fullscreen mode Exit fullscreen mode

我可能是错的，但是我更愿意一开始就用这种方法来开始一个项目，并且只有在后来证明 Flask-SQLAlchemy 扩展确实有用时才使用它。

> 这个代码可以在 github 上找到:[https://github.com/nestedsoftware/flask_sqlalchemy_starter](https://github.com/nestedsoftware/flask_sqlalchemy_starter)