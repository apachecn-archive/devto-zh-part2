# 用 C++进行优雅的错误处理

> 原文：<https://dev.to/lucpattyn/elegant-error-handling-with-c-3h1a>

优雅地处理错误是 C++长期以来所缺少的。在早期，我们使用了很多技术，比如返回 HRESULT(主要在 windows 中)或者传递一个错误对象，用错误代码和消息填充它等等。

你可能会说总有例外(扔接球..)几乎是从 C++的第一天(或第二天)开始的，但例外就是例外，应该在极端情况下谨慎使用。我从来不喜欢简单错误处理的异常，因为它们实际上会影响和改变程序的流程。所以我一直在寻找一个标准的方法来解决它。

最后，C++似乎提供了一种优雅的结构化的错误处理方式，字面上是用结构:)
(更多信息请访问:[https://en.cppreference.com/w/cpp/error/error_code](https://en.cppreference.com/w/cpp/error/error_code))

让我们先看看错误处理代码是什么样子，然后再开始讨论如何处理它！

假设我想在数据库中插入一条记录，但它抛出了一个错误，下面是它的代码:

```
    std::error_code e = db.insert(key, value);
    // using Windows MessageBox in WIN32 showing an alert dialog
    ::MessageBox(NULL, e.message().c_str(), "Error Message", 0);
    // using console
    cout << "Error Message : << e.message.c_str() << " Error Code: " << e.value();

```

这里，错误代码 e 有一个名为 *message* 的方法，它返回 std::string，您可以将它显示为人类可读的消息，也可以通过简单地调用 e.value()(例如 int err = e . value()；)

现在是时候看看如何根据你的上下文和情况形成一个错误对象了。下面的代码创建了一个 error 对象，该对象可用于返回代码中可能出现的与数据库相关的错误。

```
    struct db_err_cat : std::error_category{
            const char* name() const noexcept override{
                return "db_errors";
            }
            std::string message(int ev) const override{
                if(ev == 0){
                    return "ok";
                }
                // switch case would make the code look better tbh :)
                if(ev == 1){
                    return "db insert error";
                }else if(ev == 2){
                    return "db delete error";

                }else if(ev == 3){
                    return "db update error";
                }

                return "db unknown error";
            }
    };

    db_err_cat db_err;

```

既然我们已经创建了一个结构(read 类)来返回与数据库(db)相关的错误，那么让我们给出一种基于该结构创建一个对象的方法，该结构返回一个通用的 std::error_code 对象，该对象可以在代码的上层使用:

```
    std::error_code make_error_code(int e){
         return {e, orm_err};
     }

```

所以，在你的代码中如果一个函数需要返回一个数据库插入错误，
简单的返回 make _ error _ code(1)；

举个例子，

```
void insertIntoDB(std::string key, std::string val){
    bool b = internalDB.insert(key, val); 
    if(!b){
          return make_error_code(1);  
    }
    ....
}

```

你可能会说为什么不简单地使用像 internalDB 这样的布尔值，但是在现实世界的程序中，如果一个操作成功与否，你实际上需要比真或假更多的信息。

因此，我要么在窗口中显示消息框，要么显示类似于:
cout<<indertIntoDB(key，val)的内容。消息()。c_str()，最后 C++给出了一个标准的结构化方法来优雅地处理错误。

请注意，您只需在整个主应用程序中使用 std::error_code 对象，并从您自己构建的库中返回 std::error_code。用最少的麻烦来做错误记录变得非常容易。

我希望 C++社区能就这种优雅的错误处理方式达成一致。