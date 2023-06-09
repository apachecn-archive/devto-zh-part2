# Rust 入门:参考和寿命

> 原文：<https://dev.to/mnivoliez/getting-started-with-rust-reference-and-lifetime-1fi1>

*最初发布在我的[博客](https://www.mathieu-nivoliez.com/posts/2018-03-05-getting-started-with-rust-reference-and-lifetime)*

大家好！你一直在等待，现在你要得到它了！各位，今天我们要讲的是参考和终身！

在此之前，我想指出的是，我对人生的了解还很不全面。不过，我想我知道的已经足够给你一个大概的答案了。

## “好吧，那么...参考？”

没错。那么什么是参考呢？我暂且用一本书来比喻。假设我有一本书。

```
struct Book {
  name: &str,  
}

fn main() {
  let book = Book { name: "Clean code" };
} 
```

Enter fullscreen mode Exit fullscreen mode

现在假设我把它传给你，这样你就可以在里面读和写。

```
struct Book {
  name: &str,  
}

fn pass_to_reader(book: Book) {
  //read book
  //write in book
}

fn main() {
  let book = Book { name: "Clean code" };
  pass_to_reader(book);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，你得到了我的书的所有权。换句话说，如果你手里有这本书，我就无法与之互动。我不会读，也不会写。

```
struct Book {
  name: &str,  
}

fn pass_to_reader(book: Book) {
  //read book
  //write in book
}

fn main() {
  let mut book = Book { name: "Clean code" };
  pass_to_reader(book);
  book.name = "The Tomb, a Lovecraft story"; //this will fail as I do not have the book
} 
```

Enter fullscreen mode Exit fullscreen mode

但是你可以很容易地还书:

```
struct Book {
  name: &str,  
}

fn pass_to_reader(book: Book) -> Book {
  //read book
  //write in book
  book //the book is returned here.
}

fn main() {
  let mut book = Book { name: "Clean code" };
  let mut book = pass_to_reader(book); // shadowing, lets keep that for another post, for now it just works
  book.name = "The Tomb, a Lovecraft story"; //this will work as the book as been returned.
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是所有权的概念。简单地说，它定义了谁对什么负责。在这个例子中，书首先被传递给函数 *pass_to_reader* ，它获得了书的所有权。在函数结束时，我们返回这本书，以便 *main* 函数重新获得它的所有权。当然，这限制了与书的互动。例如，几个读者同时阅读怎么办？

## “啊，现在你终于要说参考了？”

是的，但是所有权是理解参照系的必要步骤。那么，什么是参照呢？这就好像我手里拿着一本书，打开着，这样你就可以在里面读(或写)了。简而言之，我保留书的所有权，但我让你读。为了表达这一点，我们将使用符号 **&** 。

```
struct Book {
  name: &str,  
}

fn pass_to_reader(book: &Book) {
  //read book
}

fn main() {
  let mut book = Book { name: "Clean code" };
  pass_to_reader(&book);
  book.name = "The Tomb, a Lovecraft story"; //this will work as main has kept the ownership of the book.
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到了，你可以读，但不能再写了。让我们解决这个问题！

```
struct Book {
  name: &str,  
}

fn pass_to_reader(book: &mut Book) {
  //read book
  //write book
}

fn main() {
  let mut book = Book { name: "Clean code" };
  pass_to_reader(&mut book);
  book.name = "The Tomb, a Lovecraft story"; //this will work as main has kept the ownership of the book.
} 
```

Enter fullscreen mode Exit fullscreen mode

现在好了，引用是可变的，所以你可以在书里写。

可变的引用就像是，当我拿着书的时候，我给了你一支笔让你在上面写字。这个系统有几个限制。首先，可变引用只能存在于可变绑定上。其次，一个绑定上只能有一个可变引用，这包括不可变引用。这个参照系被称为“借”，比如“你借了这本书”。

## “好吧，那一生都在那？”

终身是这个体系的第三根支柱。它断言一个借用不能比它所借用的绑定活得长。例如，假设你借了一本书，这本书有 100 页。

```
struct Page(&str);
struct Book {
  name: &str,  
  pages: [Pages], 
}

fn pass_to_reader(book: &Book) -> &Page {
  //read book
  //return ref on a specific page
  &book.pages[50]
}

fn main() {
  let mut pages = [Page("1"), ..., Page("100")]; // I ellisped the page 2 to 99... I'm not gonna write it all
  let mut book = Book { name: "Clean code", pages: pages};
  let page = pass_to_reader(&book);
  book.name = "The Tomb, a Lovecraft story"; //this will work as main has kept the ownership of the book.
  println!("{}", page.0);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是一页取决于书。因此，书页不可能比书活得长。没有书，没有书页。同样的道理也适用于参考文献:你不能让一个参考文献比书存在的时间更长。所以，你必须根据书的生命周期来表达页面的生命周期。

```
struct Page(&str);
struct Book {
  name: &str,  
  pages: [Pages], 
}

fn pass_to_reader(book: &'book Book) -> &'book Page { // we introduce the lifetime 'book here
  //read book
  //return ref on a specific page
  &book.pages[50]
}

fn main() {
  let mut pages = [Page("1"), ..., Page("100")]; // I ellisped the page 2 to 99... I'm not gonna write it all
  let mut book = Book { name: "Clean code", pages: pages};
  let page = pass_to_reader(&book);
  book.name = "The Tomb, a Lovecraft story"; //this will work as main has kept the ownership of the book.
  println!("{}", page.0);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在这里表示生存期，我们使用符号 **'** 后跟生存期的名称。
我可能在这里表达一个关键点，**生世很难对付**，所以尽量不要经常用它们。它们是强有力的工具，强大的力量意味着巨大的责任。

这就是今天的全部内容，但请注意，我们将回头再来深入探讨这个问题。下一篇关于铁锈奇妙世界的文章，我们很快会再见的。

马修