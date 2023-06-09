# 检测删除

> 原文：<https://dev.to/pauljlucas/detecting-destruction-3m0o>

假设你正在实现一个使用回调的类:

```
class operation {
public:
    struct done_callback {
        virtual ~done_callback();
        virtual void operation_done( operation *op ) = 0;
    };

    operation() { }

    bool perform( done_callback *cb );

private:
    bool success_ = false;
};

bool operation::perform( done_callback *cb ) {
    // ...
    if ( cb != nullptr )
        cb->operation_done( this );
    return success_;
} 
```

Enter fullscreen mode Exit fullscreen mode

> **先不说命名回叫**
> 
> 尽管回调似乎是仿函数的一个很好的候选，那就是:
> 
> ```
> virtual void operator()( operation *op ) = 0; 
> ```
> 
> 这样做的问题是，它限制了一个类同时实现多个回调。因此，最好给回调一个明确的名字。

进一步假设我们实现`done_callback`像:

```
struct my_callback : operation::done_callback {
    void operation_done( operation *op ) override;
};

void my_callback::operation_done( operation *op ) {
    // ...
    delete this;
    delete op;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`operation_done()`结束时，回调删除自己和`operation`,因为它已经完成了。问题是`perform()`确实:

```
 return success_;                // really: this->success_ 
```

Enter fullscreen mode Exit fullscreen mode

因此可能会转储核心，因为回调删除了`operation`。所以一般的问题是:成员函数如何判断回调是否删除了它的对象？

### 检测删除

检测删除的一种方法是让回调签名成为:

```
 virtual bool operation_done( operation *op ) = 0;
        virtual void operation_done( operation *op, bool *deleted ) = 0; 
```

Enter fullscreen mode Exit fullscreen mode

只有当回调删除了调用者时，第一个才会返回`true`；第二个可以将`*deleted`设置为`true`。虽然两者都可以工作，但它们感觉有点笨重，而且不够通用。此外，他们需要回调的合作，因此并不保险。

理想情况下，我们想知道是否调用了`~operation()`。也许让析构函数本身设置一个“析构函数调用”标志，这样`perform()`就可以检查了。但是怎么做呢？它显然不能使用`*this`的数据成员作为标志。但是它*可以*在`perform()`的堆栈帧中设置一个标志。

为此，我们需要两样东西:

1.  一个“删除检测器”(在`perform()`的堆栈框架中)，检测对象的析构函数何时被调用。
2.  “删除信号程序”(作为`operation`的数据成员)，当`~operation()`被调用时运行，并向检测器发送信号。

下面是`delete_signaler` :
的声明

```
class delete_detector;

class delete_signaler {
public:
    delete_signaler()  { }

    delete_signaler( delete_signaler const& ) {
        // intentionally do nothing
    }

    ~delete_signaler();

    delete_signaler& operator=( delete_signaler const& ) {
        // intentionally do nothing
        return *this;
    }

    void set_success( bool success );

private:
    delete_detector *detector_ = nullptr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

> 复制构造函数和赋值操作符都有意不做任何事情。具体来说，当`delete_signaler`被复制时，`detector_` *不能被复制。如果它*被*复制，那么将会发生两件坏事中的一件:*
> 
> 1.  如果在回调返回之前`delete_signaler`副本被破坏*，那么嵌入的`delete_signaler`可能会使`delete_signaler`原件发送(或未发送)的信号无效。*
> 2.  如果在回调返回后`delete_signaler`副本被销毁*，那么`detector_`将可能指向一个已经被销毁的`delete_detector`。*
> 
> 注意，仅仅不声明复制构造函数或赋值操作符是不够的，因为编译器会自动生成复制`detector_`的两者。因此，必须显式声明两个*和*不做任何事情。

`set_success()`的正文为:

```
void delete_signaler::set_success( bool success ) {
    if ( detector_ != nullptr ) {
        assert( !detector_->destructor_called() );
        detector_->status_ = success ?
            delete_detector::DTOR_CALLED_SUCCESS :
            delete_detector::DTOR_CALLED_FAILURE;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用`delete_signaler`，必须声明为`operation`的数据成员，并在销毁时调用`set_success()`:

```
class operation {
public:
    // ...
    ~operation() {
        delete_signaler_.set_success( success_ );
    }

private:
    bool success_;
    delete_signaler delete_signaler_;
}; 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，只有在像`operation`这样有“成功”结果的情况下，才需要调用`set_success()`。如果不需要这样的结果，但是调用者仍然需要检测回调是否删除了它，那么就不需要调用`set_success()`。

向探测器发送信号的所有工作都在`~delete_signaler()`中完成(稍后将详细介绍)。

下面是`delete_detector` :
的声明

```
class delete_detector {
public:
    explicit delete_detector( delete_signaler &signaler );
    ~delete_detector();

    bool destructor_called() const {
        return status_ != DTOR_NOT_CALLED;
    }

    bool success() const {
        return status_ == DTOR_CALLED_SUCCESS;
    }

private:
    enum status {
        DTOR_NOT_CALLED,
        DTOR_CALLED_VOID,
        DTOR_CALLED_SUCCESS,
        DTOR_CALLED_FAILURE
    };

    delete_signaler &signaler_;
    status status_ = DTOR_NOT_CALLED;

    delete_detector( delete_detector const& ) = delete;
    delete_detector& operator=( delete_detector const& ) = delete;

    friend class delete_signaler;
}; 
```

Enter fullscreen mode Exit fullscreen mode

A `delete_detector`的用法如下:

```
bool operation::perform( done_callback *cb ) {
    // ...
    if ( cb != nullptr ) {
        delete_detector detector{ delete_signaler_ };
        cb->operation_done( this );
        if ( detector.destructor_called() )
            return detector.success();
    }
    return success_;                // this->success_ is safe to access
} 
```

Enter fullscreen mode Exit fullscreen mode

`delete_detector`的构造函数相当简单，它只是将信号的`detector_`设置为`this` :

```
delete_detector::delete_detector( delete_signaler &signaler ) :
    signaler_{ signaler }
{
    assert( signaler_.detector_ == nullptr );
    signaler_.detector_ = this;
} 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，`delete_detector`的析构函数会通过将信号的`detector`设置回`nullptr`来进行相反的操作——但前提是信号的析构函数不是*而是*调用的:

```
delete_detector::~delete_detector() {
    if ( !destructor_called() ) {
        assert( signaler_.detector_ == this );
        signaler_.detector_ = nullptr;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一点代码是`delete_signaler`的析构函数，它将检测器的`status_`设置为`DTOR_CALLED_VOID`，但前提是它是`DTOR_NOT_CALLED`，即`set_success()`被*而不是*调用:

```
delete_signaler::~delete_signaler() {
    if ( !detector->destructor_called() )
        detector->status_ = delete_detector::DTOR_CALLED_VOID;
} 
```

Enter fullscreen mode Exit fullscreen mode