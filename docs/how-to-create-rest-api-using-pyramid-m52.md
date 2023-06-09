# 如何使用金字塔创建 REST API

> 原文：<https://dev.to/apcelent/how-to-create-rest-api-using-pyramid-m52>

[Pyramid](https://trypyramid.com/) 是遵循 MVC 架构模式的轻量级 [python](https://www.python.org/) web 框架。在本文中，我们将使用 Pyramid 建立一个 web 应用程序和 API。

金字塔是一个适合大规模 MVC 应用程序的框架，它带有灵活的引导工具。

来自 Mozilla 的库 Cornice 使得用 pyramid 开发 RESTful web 服务变得容易。

[![Alt text of image](img/e590f195dd055c42eaacede392f12b96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qGOvrmfS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/rest_api_using_pyramid.jpg)

## 安装

首先，我们创建一个虚拟环境，帮助我们将特定于项目的 python 包安装与其他项目分开。激活虚拟环境后，我们使用命令:
安装金字塔框架

```
pip install pyramid 
```

Enter fullscreen mode Exit fullscreen mode

## 创建模型

我们用一个类注释创建一个`models.py`文件，该文件映射到存储所有注释值的数据表。

```
# pyramidapp/models.py 
class Note(Base):
    __tablename__ = 'Note'
    id = Column(Integer, primary_key=True)
    title = Column(Text)
    description = Column(Text)
    create_at = Column(Text)
    create_by = Column(Text)
    priority = Column(Integer)

    def __init__(self, title, description, create_at ,create_by, priority):
        self.title = title
        self.description = description
        self.create_at = create_at
        self.create_by = create_by
        self.priority = priority

    @classmethod
    def from_json(cls, data):
        return cls(**data)

    def to_json(self):
        to_serialize = ['id', 'title', 'description', 'create_at', 'create_by', 'priority']
        d = {}
        for attr_name in to_serialize:
            d[attr_name] = getattr(self, attr_name)
        return d 
```

Enter fullscreen mode Exit fullscreen mode

## 视图

在`views.py`文件中，我们为不同的 API 请求添加服务。

```
@resource(collection_path='/notes', path='/notes/{id}')
class NoteView(object):

    def __init__(self, request):
        self.request = request

    def collection_get(self):

        return {
            'notes': [
                {'id': note.id, 'title': note.title, 'description': note.description,
                'create_at': note.create_at, 'create_by': note.create_by, 'priority': note.priority}

                    for note in DBSession.query(Note)

                    ]
            }

    def get(self):

        try:
            return DBSession.query(Note).get(
                int(self.request.matchdict['id'])).to_json()
        except:
            return {}

    def collection_post(self):

        note = self.request.json
        DBSession.add(Note.from_json(note))

    def put(self):
        try:
            obj=DBSession.query(Note).filter(Note.id==self.request.matchdict['id'])
            obj.update(self.request.json)
            return {'notes': [
                    {'id': note.id, 'title': note.title, 'description': note.description,
                    'create_at': note.create_at, 'create_by': note.create_by, 'priority': note.priority}

                        for note in DBSession.query(Note)

                        ]
                    }
        except:
            return {'result': 'No object found'}

    def delete(self):
        obj=DBSession.query(Note).filter(Note.id==self.request.matchdict['id']).first()
        DBSession.delete(obj)

        return {'notes': [
                {'id': note.id, 'title': note.title, 'description': note.description,
                'create_at': note.create_at, 'create_by': note.create_by, 'priority': note.priority}

                    for note in DBSession.query(Note)

                    ]
                } 
```

Enter fullscreen mode Exit fullscreen mode

## 运行应用程序

通过执行
创建数据库模式

```
python initialize_db.py 
```

Enter fullscreen mode Exit fullscreen mode

通过
启动开发服务器

```
python setup.py develop
pserve note.ini --reload 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过在浏览器中导航到 URL[http://localhost:6543/notes](http://localhost:6543/notes)来查看笔记。

打开 python shell 并执行 API 请求:

```
requests.post('http://localhost:6543/notes',
                 headers={'Content-Type': 'application/json'},
                data=json.dumps({   "title": "sample note one ",
                                    "create_at": "2017-08-23 00:00",
                                    "create_by": "apcelent",
                                    "description": "sample notes",
                                    "priority": 3,

                                }))

requests.put('http://localhost:6543/notes/1',
                 headers={'Content-Type': 'application/json'},
                data=json.dumps({   "title": "sample note edit ",
                                    "create_at": "2017-08-23 00:00",
                                    "create_by": "apcelent",
                                    "description": "sample notes edit",
                                    "priority": 4,

                                }))

requests.delete('http://localhost:6543/notes/1') 
```

Enter fullscreen mode Exit fullscreen mode

源代码可以在 [github](https://github.com/codecraf8/rest-api-python-pyramid) 上找到

希望这篇文章有所帮助！

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/create-rest-api-using-pyramid.html)上。