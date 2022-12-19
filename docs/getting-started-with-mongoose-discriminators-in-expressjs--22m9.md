# Express.js 中的 Mongoose 鉴别器入门

> 原文：<https://dev.to/helenasometimes/getting-started-with-mongoose-discriminators-in-expressjs--22m9>

我最近开始重写我的 Rails 项目，来表达。一个原因是我想更好地掌握 JavaScript，另一个原因是 Rails 感觉非常神奇，我不喜欢我在使用它时不知道自己在做什么(“它工作，但我不确定为什么”)。这不一定是一件坏事，这是可以通过深入 Rails 解决的问题，但老实说，我对这样做不感兴趣。

所以对于这次重写，我想在存储数据方面更深入一点，不再依赖幕后的魔法。这意味着提出一个合适的数据模型。我想要一个灵活的 NoSQL 数据库(我以后可能需要添加不同的集合类型！).我选择了 MongoDB，因为它与 Node 配合得很好，还因为我想尝试 MongooseJS(查看文档，它似乎提供了一个易于理解的抽象层和剧透警告:它非常简洁)。

## 免责声明

我写这篇文章是因为我正在学习，我对提到的任何概念的理解都可能是错误的。如果你认为是那样的话，一定要让我知道😃

## 问题

想象一下，你正在追踪某一年你看了哪些电影、书籍和电视节目。这三个东西有几个共同点:都有标题和发布日期。然而，它们也彼此不同:一本书有一个*作者*，而一部电影有一个*导演*。一部电视剧没有这些东西，但是它有一个*季*。那么，如何设置您的 Mongoose 模式呢？你可以很容易地为每一个创建三种不同的模式(书籍、电影和电视节目)。然而，您会重复自己——在每个模式中，您会有相同的标题字段和发布日期字段。如果您想添加所有三个模式共有的另一个字段，比如它是否是重新观察/重新读取(“重做”)，您必须将这个新字段添加到三个不同的文件中。

如果你可以扩展某种“基础”模式，让电影、书籍和电视节目继承这个模式，会怎么样？我不知道怎么做，但幸运的是，一位同事建议我调查猫鼬歧视者。不幸的是，文档有点稀疏，我找不到任何 Express.js 特定的教程/博客帖子，所以这里是我尝试解决这个问题的方法。希望这篇文章能帮助那些想在他们的 Express 应用程序中集成猫鼬识别器的人:)

## 非干之道

为了清楚起见，这是我们的模式在没有鉴别器的情况下的样子:

```
> models/book.js

// Define our Book schema
const BookSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    author: { type: String, required: true },
    release_date: { type: Date, required: true },
  }
);

// Create a model from our schema
module.exports = mongoose.model('Book', BookSchema); 
```

Enter fullscreen mode Exit fullscreen mode

```
> models/movie.js

const MovieSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    director: { type: String, required: true },
    release_date: { type: Date, required: true },
  }
);

module.exports = mongoose.model('Movie', MovieSchema); 
```

Enter fullscreen mode Exit fullscreen mode

```
> models/tvshow.js

const Tvshow = new mongoose.Schema(
  {
    title: { type: String, required: true },
    season: { type: Number, required: true },
    release_date: { type: Date, required: true },
  }
);

module.exports = mongoose.model('Tvshow', TvshowSchema); 
```

Enter fullscreen mode Exit fullscreen mode

这没什么不对！然而，就像我之前提到的，如果我们想要添加一个新的属性，比如:

```
// signals whether I've already seen or read the item in question
redo: { type: Boolean, required: false } 
```

Enter fullscreen mode Exit fullscreen mode

我们必须在三个独立的文件中添加三次😖。所以让我们尝试一些不同的东西。

我们将创建一个名为`Base`的‘主’模式，并让`Book`、`Movie`和`Tvshow`继承它。这就是我们想要实现的伪代码:

```
Base:
    title: { type: String, required: true },
    date_released: { type: Date, required: true },
    redo: { type: Boolean, required: false },

Book:
    Inherit everything from Base, and add the following just for this schema:
    author: { type: String, required: true }

Movie:
    Inherit everything from Base, and add the following just for this schema:
    director: { type: String, required: true }

TV Show:
    Inherit everything from Base, and add the following just for this schema:
    season: { type: Number, required: true } 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们如何给我们的子模式(书籍、电影、电视节目)提供`Base`选项呢？换句话说，我们将如何扩展我们的`Base`？进入[鉴别器](http://mongoosejs.com/docs/discriminators.html)。鉴别器是`model`的一个函数，*返回一个模型，其模式是基础模式和鉴别器模式的并集。*所以基本上，鉴别器将允许我们指定一个键，像`kind`或`itemtype`。有了这个密钥，我们可以存储不同的实体(书籍、电影、电视节目..)在一个集合中，我们仍然能够区分这些实体。

因此，让我们建立我们的基础模式。同样，这是我们的其他模式将扩展的结构。

```
const baseOptions = {
  discriminatorKey: 'itemtype', // our discriminator key, could be anything
  collection: 'items', // the name of our collection
};

// Our Base schema: these properties will be shared with our "real" schemas
const Base = mongoose.model('Base', new mongoose.Schema({
      title: { type: String, required: true },
      date_added: { type: Date, required: true },
      redo: { type: Boolean, required: false },
    }, baseOptions,
  ),
);

module.exports = mongoose.model('Base'); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以这样编辑`book.js`:

```
> models/book.js

const Base = require('./base'); // we have to make sure our Book schema is aware of the Base schema

const Book = Base.discriminator('Book', new mongoose.Schema({
    author: { type: String, required: true },
  }),
);

module.exports = mongoose.model('Book'); 
```

Enter fullscreen mode Exit fullscreen mode

对于`Base.discriminator()`，我们告诉 Mongoose 我们想要获得`Base`的属性，并添加另一个`author`属性，只用于我们的图书模式。让我们用`models/movie.js` :
做同样的事情

```
> models/movie.js

const Base = require('./base');

const Movie = Base.discriminator('Movie', new mongoose.Schema({
    director: { type: String, required: true },
  }),
);

module.exports = mongoose.model('Movie'); 
```

Enter fullscreen mode Exit fullscreen mode

和`tvshow.js` :

```
> models/tvshow.js

const Base = require('./base');

const Tvshow = Base.discriminator('Tvshow', new mongoose.Schema({
    season: { type: Number, required: true },
  }),
);

module.exports = mongoose.model('Tvshow'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们为我们的集合创建一本新书，新书实例将像这样出现在我们的 MongoDB 集合中:

```
{
    "_id": {
        "$oid": "unique object ID"
    },
    "itemtype": "Book", 
    "author": "Book Author 1",
    "title": "Book Title 1",
    "date_added": {
        "$date": "2018-02-01T00:00:00.000Z"
    },
    "redo": false,
} 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？现在让我们获取一些数据。下面的例子将返回我们收藏的书籍数量，以及所有电视节目及其标题和季节:

```
> controllers/someController.js

const Book = require('../models/book');
const Tvshow = require('../models/tvshow');
const async = require('async');

exports.a_bunch_of_stuff = function(req, res) {
    async.parallel({
        book_count: function (callback) {
            Book.count(callback);
        },
        tvshow_all: function(callback) {
            Tvshow.find({}, 'title season', callback)
        },
    }, function(err, results) {
        res.render('index', { error: err, data: results });
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

通过使用鉴别器，我们有四个包含干代码的小文件，而不是三个包含大量相同代码的大模型文件😎现在，每当我想添加一个跨模式共享的新属性时，我只需编辑`Base`。如果我想增加新的模特(也许我应该开始记录我去的演唱会！)，我可以在需要时轻松扩展现有属性。