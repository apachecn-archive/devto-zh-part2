# Python 和 JavaScript 中的等价物。第四部分

> 原文：<https://dev.to/djangotricks/equivalents-in-python-and-javascript-part-4-3mda>

[![](img/738abaded11fa60aeeccf98e353a5f89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uyYlqR0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-IUXEO64Y6q8/WzrC947Je8I/AAAAAAAAB58/KyzGfiel_6AxNOHLu4lHsZhTPyznTvmfQCLcBGAs/s1600/equivalents-in-python-and-javascript-part-4.png)

在关于用 **Python** 和 **JavaScript** 进行类比的系列文章的[最后](https://dev.to/djangotricks/equivalents-in-python-and-javascript-part-3-4icc)的三个部分中，我们探索了许多有趣的概念，比如序列化为 JSON、错误处理、使用正则表达式、字符串插值、生成器、lambdas 等等。这次我们将深入研究函数参数，创建类，使用类继承，以及定义类属性的 getters 和 setters。

## 函数参数

**Python** 对于函数的参数处理非常灵活:你可以在那里设置默认值，允许灵活的位置或关键字参数(`*args`和`**kwargs`)。当您将值传递给函数时，您可以通过名称来定义该值应该分配给哪个参数。所有这些在某种程度上现在在 **JavaScript** 中也是可能的。

**Python** 中函数参数的默认值可以这样定义:

```
from pprint import pprint

def report(post_id, reason='not-relevant'):
    pprint({'post_id': post_id, 'reason': reason})

report(42)
report(post_id=24, reason='spam') 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中同样可以实现:

```
function report(post_id, reason='not-relevant') {
    console.log({post_id: post_id, reason: reason});
}

report(42);
report(post_id=24, reason='spam'); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在 **Python** 中，可以使用`*`操作符接受位置参数，如下所示:

```
from pprint import pprint

def add_tags(post_id, *tags):
    pprint({'post_id': post_id, 'tags': tags})

add_tags(42, 'python', 'javascript', 'django') 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，可以使用`...`操作符:
接受位置参数

```
function add_tags(post_id, ...tags) {
    console.log({post_id: post_id, tags: tags});
}

add_tags(42, 'python', 'javascript', 'django'); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

当您希望允许灵活的选项数量时，关键字参数经常在 **Python** 中使用:

```
from pprint import pprint

def create_post(**options):
    pprint(options)

create_post(
    title='Hello, World!', 
    content='This is our first post.',
    is_published=True,
)
create_post(
    title='Hello again!',
    content='This is our second post.',
) 
```

Enter fullscreen mode Exit fullscreen mode

向一个 **JavaScript** 函数传递多个可选参数的常见做法是通过一个字典对象，例如`options`。

```
function create_post(options) {
    console.log(options);
}

create_post({
    'title': 'Hello, World!', 
    'content': 'This is our first post.',
    'is_published': true
});
create_post({
    'title': 'Hello again!', 
    'content': 'This is our second post.'
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 类和继承

Python 是一种面向对象的语言。由于 ECMAScript 6 标准的支持，也可以用 JavaScript 编写面向对象的代码，而不需要黑客和奇怪的原型语法。

在 **Python** 中，你可以用构造函数和方法创建一个类，以文本形式表示它的实例，如下:

```
class Post(object):
    def __init__(self, id, title):
        self.id = id
        self.title = title

    def __str__(self):
        return self.title

post = Post(42, 'Hello, World!')
isinstance(post, Post) == True
print(post)  # Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，要创建一个带有构造函数的类和一个以文本形式表示其实例的方法，您应该写:

```
class Post {
    constructor (id, title) {
        this.id = id;
        this.title = title;
    }
    toString() {
        return this.title;
    }
}

post = new Post(42, 'Hello, World!');
post instanceof Post === true;
console.log(post.toString());  // Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在我们可以在 **Python** 中创建两个类`Article`和`Link`，它们将扩展`Post`类。在这里你还可以看到我们如何使用`super`从基类`Post`中调用方法。

```
class Article(Post):
    def __init__(self, id, title, content):
        super(Article, self).__init__(id, title)
        self.content = content

class Link(Post):
    def __init__(self, id, title, url):
        super(Link, self).__init__(id, title)
        self.url = url

    def __str__(self):
        return '{} ({})'.format(
            super(Link, self).__str__(),
            self.url,
        )

article = Article(1, 'Hello, World!', 'This is my first article.')
link = Link(2, 'DjangoTricks', 'https://djangotricks.blogspot.com')
isinstance(article, Post) == True
isinstance(link, Post) == True
print(link)
# DjangoTricks (https://djangotricks.blogspot.com) 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，同样的事情也可以通过下面的代码来实现:

```
class Article extends Post {
    constructor (id, title, content) {
        super(id, title);
        this.content = content;
    }
}

class Link extends Post {
    constructor (id, title, url) {
        super(id, title);
        this.url = url;
    }
    toString() {
        return super.toString() + ' (' + this.url + ')';
    }
}

article = new Article(1, 'Hello, World!', 'This is my first article.');
link = new Link(2, 'DjangoTricks', 'https://djangotricks.blogspot.com');
article instanceof Post === true;
link instanceof Post === true;
console.log(link.toString());
// DjangoTricks (https://djangotricks.blogspot.com) 
```

Enter fullscreen mode Exit fullscreen mode

## 类属性:getters 和 setters

在面向对象编程中，类可以有属性、方法和特性。属性是属性和方法的混合。您将它们作为属性处理，但是在后台，它们调用特殊的 getter 和 setter 方法，在设置或返回给调用者之前以某种方式处理数据。

在 **Python** 中`slug`属性的 getters 和 setters 的基本线框如下:

```
class Post(object):
    def __init__(self, id, title):
        self.id = id
        self.title = title
        self._slug = ''

    @property
    def slug(self):
        return self._slug

    @slug.setter
    def slug(self, value):
        self._slug = value

post = new Post(1, 'Hello, World!')
post.slug = 'hello-world'
print(post.slug) 
```

Enter fullscreen mode Exit fullscreen mode

在 **JavaScript** 中，`slug`属性的 getters 和 setters 可以定义为:

```
class Post {
    constructor (id, title) {
        this.id = id;
        this.title = title;
        this._slug = '';
    }

    set slug(value) {
        this._slug = value;
    }

    get slug() {
        return this._slug;
    }
}

post = new Post(1, 'Hello, World!');
post.slug = 'hello-world';
console.log(post.slug); 
```

Enter fullscreen mode Exit fullscreen mode

## 外卖

*   在这两种语言中，您都可以定义函数的默认参数值。
*   在这两种语言中，您可以为函数传递灵活数量的位置或关键字参数。
*   在这两种语言中，面向对象编程都是可能的。

你可能已经注意到了，我提供了一个备忘单，上面有你在这里看到的关于 **Python** 和 **JavaScript** 的完整列表。至少对我来说，在我的笔记本电脑旁边放一张打印好的有价值信息的纸要方便得多，而不是在窗口或标签之间切换，滚动以获得正确的片段。因此，我鼓励您获取此备忘单，并改进您的编程！

<center>[✨✨✨
Get the Ultimate Cheat Sheet of
Equivalents in Python and JavaScript
✨✨✨](https://www.djangotricks.com/goodies/YbnpiLKBmAZi/)</center>

好好利用它！