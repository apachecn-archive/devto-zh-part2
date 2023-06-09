# 处理复杂的 MVC 应用程序——如何扩展和避免控制器混乱

> 原文：<https://dev.to/pavlosisaris/handling-complex-mvc-applications----how-to-scale-and-avoid-controller-chaos-lb9>

###### 帖子封面图片由 undabot.com[提供](https://undabot.com/)

*本文使用 [Laravel](https://laravel.com/) 作为代码片段，但是这个范例可以很容易地适用于其他 MVC 框架。*

*为了让事情变得更有趣，我们将通过张贴两位专业人士之间的一段虚构对话来展示这篇文章:*

Stan ，一位经验丰富的开发人员，他犯了许多架构错误(但谢天谢地，他似乎正在从中吸取教训)

**Ollie** ，开发新手，刚开始钻研严肃编程的世界，有一些简单的应用。

对话是这样进行的:

# 简介

嘿，奥利！今天我想和你谈谈 [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 。MVC 是构建健壮应用程序的流行架构模式。

**【奥利】**等等等等… MVC？那是什么？

**【Stan】**MVC 代表[模型-视图-控制器](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。它本质上定义了健壮代码设计的策略。它将应用程序分解成不同的部分，以便在应用程序的模块之间实现更高的关注点分离。当试图增加更多的功能或维护它时，模块化的代码非常有用。
我们来看下图:

[![MVC framework layout](img/fd347d34ced9c9cfaa8a8ec83e8ccc3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YaJNR2sH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/a/a0/MVC-Process.svg)

###### *图片取自[维基百科](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)*

**【Stan】**因此，用户与视图进行交互，在每次交互时，调用相关的控制器方法，然后这些方法负责更新/获取适当的数据返回给用户(再次使用视图层)。

抓住你了！我已经在很多项目中使用了 MVC。我知道很多！

## 典型代码示例

不要太快，奥利，还有很多要讲的。
现在，让我们看看一个典型教程中的 Laravel 控制器方法，我们通常会偶然发现:

```
 <?php  
namespace App\Http\Controllers;  

class HomeController extends Controller {  

  public function simpleMethod() {  

    $books = Books:all();
    foreach($books as $book) {
        // some business logic here...
    }
    return view('home.home')->with(['books' => $books]);  
  }  
} 
```

好了，这段代码看起来很简单！

如果我们能把它分解成单个的组件，这样会更好吗？

我们到底为什么要这么做？我喜欢那个代码；简单而有效。它有什么特别之处？

我年轻的学徒，它有一些非常特别的东西！它被称为“复杂应用程序”。
看，在一个相对小的项目中，在一个控制器方法中包含所有东西是非常好的。

**【奥利】**等等等等……你说的一切……是什么意思？

## 层层营救

我感觉你没有理解大多数应用程序是如何工作的…在一个典型的 MVC 应用程序中，我们有以下几层:

*   验证层

*   业务逻辑层

*   数据库/存储库层

*   错误处理层

*   成功/错误显示层

老实说，MVC 应用程序可以进一步分解成更多的层，但这些是最基本的层。如果大部分教程都省略了它们，那就再好不过了。他们的工作不是解释如何对应用程序进行分层，而是解释教程的主题。

好吧，那我为什么要分解我的申请呢？如果我想添加一些功能，我可以在我的控制器方法中完成。

## 随着时间腐烂的代码

**【Stan】**当然你可以继续这样做，但是在某个时候会导致代码重复，控制器方法和类特别大，代码普遍腐烂。

这就是为什么我说向下分层在复杂的应用程序中效果更好。

如果您不打算扩展您的项目，那么您可以将一切留在您的控制器方法中。

随着应用程序的增长，维护变得越来越困难。随着复杂性的增加，可重用模块的价值也在增加。我们知道，在冒技术债务的风险之前，我们必须做些什么，这反过来会导致更难的维护和进一步的开发。

在添加更多功能一段时间后，让我们看看上一个例子中的代码；

```
 <?php  
namespace App\Http\Controllers;  

class BooksController extends Controller {

    public function complexMethod(HttpRequest $request) {

        $authorId = $request->author_id;
        if(!Author::find($authorId))
            // wrong data
            return back()->with('error','Wrong data');
        try {

            // this DB query needs to be duplicated if we want
            // to use it in another part of the code
            $books = Books::with('autor')
            ->with('reviews')->where(['author_id' => $authorId])
            ->get();

            foreach($books as $book) {
                // some business logic here...
                // this code snippet can turn out to be huge,
                // since it grows with the application complexity.
            }

            return view('home.home')->with(['books' => $books]);
        } catch (Exception $e) {
            return back()->with('error', $e->getMessage());
        }
    }
} 
```

**【奥利】**啧啧，你说对了！我希望我的应用程序能够扩展！如何将我的代码分成更多的层？

我也这么想。本质上，控制器的工作是与视图(MVC 中的 V)层进行交互。**这意味着它应该处理用户输入，并返回要显示给用户的数据。**

不多不少。

## 每层谁负责？

说到这里，让我们修改层，看看哪些应该留在控制器中，哪些应该分开:

*   **验证层**
    这一层负责验证用户输入的数据。在 MVC 图中，它离视图层很近，所以应该在控制器类中实现。

*   **业务逻辑层**
    随着时间的推移，这一层通常会变得过于复杂。它定义了应用程序的业务规则，与视图无关。所以，我们需要将它从控制器中解耦出来，打包到业务逻辑层的另一个类中。

*   **数据库/存储库层**
    这一层包括应用程序的数据库查询。在许多数据密集型的复杂应用程序中(如实时系统)，这一层本身也可能是一个不同的应用程序。所以它不应该在控制器中实现，而应该在位于数据库/存储库层的另一个类中实现。

*   **错误处理层**
    抛出异常时我们该怎么办？看情况。也许我们希望将异常记录到日志通道中，并采取特殊的操作。
    在大多数 MVC 应用程序中，我们希望通知用户错误，所以这一层应该在业务逻辑层和控制器层都实现**。**

***   **成功/错误显示层**
    该层与前一层耦合。当操作成功时，或者当出现异常时，相应地通知用户是非常重要的。这一层定义在控制器和视图之间，可以在控制器类中实现。** 

 **哇，我学到了很多！但我还是有点困惑；我的控制器现在应该是什么样子？

## 分层编码

**【斯坦】**酷问题！请看下面的例子:

```
 <?php
namespace App\Http\Controllers;

class BooksController extends Controller {

  protected $bookManager;

  function __construct() {
      $this->bookManager = new BookManager();
  }

  public function complexMethod(HttpRequest $request) {
      // VALIDATION LAYER
      // having all rules in a separate validationRules method
      // allows reusage
      $validator = Validator::make($request->all(), $this->validationRules($request));

      if ($validator->fails()) {
          return back()->with('error','Wrong data');
      }

      try {
          // BUSINESS LOGIC LAYER
          $books = $this->bookManager->getAllBooksForAuthor($request->author_id);

          // SUCCESS DISPLAY LAYER
          return view('home.home')->with(['books' => $books]);
      } catch (Exception $e) {
            //ERROR DISPLAY LAYER
        return back()->with('error', $e->getMessage());
      }

  }

} 
```

**【Ollie】**但是业务逻辑方法和数据库/存储库层方法在哪里呢？

**【Stan】**当然是其他类中定义的！面向对象编程最好的一点是能够将不同的模块打包成单独的类，并通过创建这些类的实例来使用它们。
(参见最后一个例子中的构造函数)。

让我们看看我们的`BookManager class` :

```
 <?php  
namespace App\BusinessLogicLayer\;
use App\StorageLayer\BookRepository;

class BookManager {

    protected $bookRepository;
    const PUBLISHED_BOOK_STATE = 1;

    public function __construct() {
        $this->bookRepository = new BookRepository();
    }

    public function getAllBooksForAuthor($authorId) {
        // here we can add all the business logic:
        // for example, we can check whether the author has any 
        // books that are in a DRAFT state, or we can check if the
        // author is the same user as the logged in user, in order
        // to display more data.

        $books = $this->booksRepository->getAllBooks([
            'state' => self::PUBLISHED_BOOK_STATE,
            'author_id' => $authorId
        ]);   

        foreach($books as $book) {
            // business logic here
        }

        return $books;
    }
} 
```

但是我在这里也没有看到任何 DB 查询。

**【斯坦】**正是！数据库查询在存储库/存储层，还记得吗？看下面这个类:

```
 <?php 

namespace app\StorageLayer;
use app\models\Book;

class BookRepository {

    public function getAllBooks($attributesArray) {  
        return Book::where(attributesArray);  
    }

} 
```

## 思考未来

**【奥利】**这个班不是很小吗？难道我们不能省略它，将 DB 查询包含到业务逻辑层吗？

如果我们想正确地伸缩，就不会。请记住，随着项目的增长，我们的类中可能需要复杂的 DB 查询，或者甚至在一个完全独立的项目中转移存储库层(甚至在不同的服务器中)。

**【奥利】**哇，这些我还真没想到！你是对的。这段代码看起来干净且可伸缩。我想这使得添加更多的功能更加容易。

**【斯坦】**正是。在复杂的应用中，遵循[开闭原则](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)至关重要:

*“软件实体(类、模块、功能等。)应该对扩展开放，但对修改关闭"*

因此，即使您很久以前已经编写了一个控制器方法，并且想要编写另一个重用其他类中定义的一些层的方法，您也可以简单地在新的控制器方法中调用相关的方法。

那太酷了！我觉得现在可以扩展了！

[![Scale Projects Meme](img/749b686396f48f180b76675846191a06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c3q-K-ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2ku5ql.jpg)

[![enter image description here](img/ebb95a4b64f547033a59a09d0ccc83ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MMqvuXkF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2ku5ur.jpg)**