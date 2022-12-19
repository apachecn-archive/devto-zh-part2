# 在 SQLAlchemy 中实现模型类继承

> 原文：<https://dev.to/ghost/implementing-model-class-inheritance-in-sqlalchemy-3f05>

### 在 SQLAlchemy 中实现模型类继承(带 Flask)

SQLAlchemy 是为 Python 编程语言设计的对象关系映射(ORM)。ORM 理论上允许程序员抽象出 SQL。简单地说，它们允许我们使用纯 Python(对象/函数)与数据库进行交互。对于我的例子，我将使用 flask-SQLAlchemy 扩展。

每个表被称为一个模型，每个模型只是一个 python 类，该类的每个属性成为 SQL 表中的一列。数据库由多个模型组成。就像普通的 Python 模型一样，可以从其他模型继承，并与父模型共享属性。如果您有存储相似类型数据的模型，这将非常有用。

### [T1】models . py](#modelspy)

<figure>[![](../Images/e8431ae720302905993fec5f67cb8184.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vRE4EawA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1E_1njdS7Fu4sQmMzhBikQ.png)

<figcaption>models . py</figcaption>

T6】</figure>

看一下 *models.py* 模块，我们定义了一个名为 Pets 的抽象类。这意味着 SQLAlchemy 不会为该模型创建表。我们接下来的两个模型猫和狗继承了宠物的所有属性。因此，猫和狗的表都有一个名为名称、价格和品种的列。

这样做的主要好处是，如果您需要更改模型，只需在一个地方进行更改。从基础模型继承的模型越多。

### __init__。巴拉圭

<figure>[![](../Images/2d0f72f1901d372853affb8b7382c7e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t0WT8D7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMsBSLeUl8FY5n1s6nwuwtQ.png) 

<figcaption>【初始 __】。py</figcaption>

</figure>

上面是一个例子 *__init__。py* 文件初始化数据库，并从模型中创建所有数据库表。就这些了，谢谢阅读。

**请注意**用 SQLAlchemy 实现继承还有其他方法，我个人认为这种方法在代码可读性方面是最干净的。

### 附录

*   [示例源代码](https://github.com/hmajid2301/medium/tree/master/Implementing%20Model%20Class%20Inheritance%20in%20SQLAlchemy%20(with%20Flask))
*   [SQL 语法](https://www.sqlalchemy.org/)
*   [烧瓶-sqlalchemy](http://flask-sqlalchemy.pocoo.org/2.3/)