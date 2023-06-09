# 如何在 reactjs 中的组件之间传递状态

> 原文：<https://dev.to/zeyadetman/how-to-pass-state-between-components-in-reactjs-2pg1>

在这篇文章中，我将解释如何在 Reactjs 中的组件之间传递状态。
我们将建立一个小型的“你读了多少本书？”app，在这个应用程序中，我们
有两个主要组件，一个大的称为“图书馆”,另一个小的称为“书”,我们有 3 本书处于图书馆状态，每本书都有自己的状态。勾选任何一本书，算作已读。[试试这里的应用](https://zeyadetman.github.io/howmanybooks/)

**让我们从代码开始:**

## 将状态从父传递给子

在我们的库组件中，我们有这个状态

```
this.state = {
    reads: 0,
    books: [
        {
            name: 'Zero to one',
            isbn: '9780804139298',
            author: 'Peter Thiel',
            cover: 'https://images.gr-assets.com/books/1414347376l/18050143.jpg',
            status: false
        },
        {
            name: "The Manager's Path",
            isbn: '9781491973899',
            author: 'Camille Fournier',
            cover: 'https://images.gr-assets.com/books/1484107737l/33369254.jpg',
            status: false
        },
        {
            name: 'Calculus, Better Explained',
            isbn: '9781470070700',
            author: 'Kalid Azad',
            cover: 'https://images.gr-assets.com/books/1448590460l/27993945.jpg',
            status: false
        }
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

并且想要创建`this.state.books.length` -作为数字- `Book`组件每个都有来自`Library`组件状态的`books`数组的道具。我们必须处理这两个部分。

**首先**对于父组件，我们必须多次创建`Book`组件`this.state.books.length`，并像这样将我们不同的值传递给它们:

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Library/Library.jsx)

```
{
    this.state.books.map((_book, _id) => {
        return (
            <Book
                handleCounter={this.handleCounter}
                key={_id}
                id={_book.isbn}
                name={_book.name}
                isbn={_book.isbn}
                author={_book.author}
                cover={_book.cover}
            />
        );
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意*暂时忽略`handleCounter`。

**第二个**对于孩子`Book`，我们有来自父母的值，让我们使用它们:

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Book/Book.jsx)

```
...
render() {
    return (
        <Card>
            <Image
                src={this.props.cover}
                alt="Book cover"
... 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们从父组件`Library`创建了 3 个`Book`组件，并从父状态设置它们的值。
简单！对吗？
太好了，我们开始第二部分吧

## 将状态从子节点传递到父节点

在这一部分，我们希望通过选中每本书的复选框来处理您阅读的书籍数量。

在我们的`Book`中我们有这种状态

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Book/Book.jsx)

```
this.state = {
    status: false,
    id: this.props.id
}; 
```

Enter fullscreen mode Exit fullscreen mode

*注意*不要忘记将`props`传递给组件的构造函数。

`status`表示你是否读过这本书，它的默认值是`false`，`id`是这本书的 id，我像我们在上一节中学习的那样用图书 id 来设置它。

我们需要处理这个状态的变化，然后更新父状态中的 books 数组。

在我们的`Book`组件中，我们将添加一个复选框来接收图书状态的变化，并将`this.handleChange`传递给它的`onChange`事件，如下所示:

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Book/Book.jsx)

```
<input type="checkbox" name="example" onChange={this.handleChange} /> 
```

Enter fullscreen mode Exit fullscreen mode

您需要首先绑定函数，然后用新状态更新`Book`状态，更新子状态后，我们将像这样更新父状态`Library`:

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Book/Book.jsx)

```
handleChange() {
        this.setState({status: !this.state.status}, this.updateLibraryCount);
    }

updateLibraryCount() {
        this.props.handleCounter(this.state);
    } 
```

Enter fullscreen mode Exit fullscreen mode

在`updateLibraryCount`中，我们使用了`Library`的`handleCounter`功能作为道具，然后将`Book`状态传递给它，现在`Library`看到了`Book`状态，太棒了！让我们使用它。

[完整代码此处](https://github.com/zeyadetman/howmanybooks/blob/master/src/components/Library/Library.jsx)

```
handleCounter(_State) {
        //Get the index of this book by searching by its unique isbn
        const ObjNum = this.state.books.findIndex(
            _book => _book.isbn === _State.id
        );

        //then update its value in the Library component
        this.setState(
            {
                books: update(
                    ObjNum,
                    {...this.state.books[ObjNum], status: _State.status},
                    this.state.books
                )
            },
            () => {
                //this is a callback to handle the new change of the book status and increment the reads
                const _read = this.state.books.filter(_book => _book.status === true)
                    .length;
                this.setState({reads: _read});
            }
        );
    } 
```

Enter fullscreen mode Exit fullscreen mode

我希望你明白如何将状态从父母传递到孩子，反之亦然，这里是[的完整代码](https://github.com/zeyadetman/howmanybooks)，这是[在我的博客](https://zeyadetman.github.io/projects/2018/07/25/How-to-pass-state-between-components-in-reactjs.html)上的原始帖子。如果你有任何问题，欢迎在评论中提问或者发[邮件](//zeyadetman@gmail.com)。