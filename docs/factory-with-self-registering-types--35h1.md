# 具有自注册类型的工厂

> 原文：<https://dev.to/fenbf/factory-with-self-registering-types--35h1>

[![Factory with self registering types](../Images/840329b6917675aac5540da6db85769f.png)T2】](http://www.bfilipek.com/2018/02/factory-selfregister.html)

编写工厂方法可能很简单:

```
unique_ptr<IType> create(name) {
    if (name == "Abc") return make_unique<AbcType>();
    if (name == "Xyz") return make_unique<XyzType>();
    if (...) return ...

    return nullptr;
} 
```

Enter fullscreen mode Exit fullscreen mode

只有一个开关/if，然后在一个匹配后，你返回一个正确的类型。

但是如果我们事先不知道所有的类型和名称呢？或者我们什么时候想让这样的工厂更通用？

让我们看看类如何在工厂中注册自己，以及使用它的例子有哪些。

**注意:**本文来自我的博客: [Bartek 的编码博客:自注册类型的工厂](http://www.bfilipek.com/2018/02/factory-selfregister.html)。

## [T1】简介](#intro)

当您有一个相对简单的应用程序时，本文开头的示例中显示的代码是正确的。例如，在我对 pimpl 的[实验中，我的第一个代码版本包含:](http://www.bfilipek.com/2018/01/pimpl-interface.html) 

```
static unique_ptr<ICompressionMethod> 
Create(const string& fileName)
{
    auto extension = GetExtension(filename);
    if (extension == "zip")
        return make_unique<ZipCompression>();
    else if (extension = "bz")
        return make_unique<BZCompression>();

    return nullptr;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我想根据文件名的扩展名创建`ZipCompression`或`BZCompression`。

这个简单的解决方案在一段时间内对我有效。但是，如果您想进一步发展应用程序，您可能会遇到以下问题:

*   每次你写一个新的类，你想在工厂中包含它，你必须在`Create()`方法中添加另一个 if。在复杂的系统中容易忘记。
*   工厂必须知道所有的类型
*   在`Create()`中，我们任意使用字符串来表示类型。这种表示仅在单个方法中可见。如果你想在别的地方用呢？字符串可能很容易拼错，特别是当你有几个地方需要比较的时候。

总而言之，我们在工厂和类之间有很强的依赖性。

但是如果类可以自己注册呢？那会有帮助吗？

*   工厂只是做它的工作:基于一些匹配创建新的对象。
*   如果你写一个新的类，没有必要改变工厂类的某些部分。这样的类将自动注册。

这听起来是个好主意。

## 实际例子

为了给你们更多的动力，我想展示一个真实的例子:

### 谷歌测试

当你使用谷歌测试库时，你写:

```
TEST(MyModule, InitTest)
{
} 
```

Enter fullscreen mode Exit fullscreen mode

这单`TEST`宏的背后发生了很多事情！

对于初学者来说，您的测试被扩展到一个单独的类中——所以每个测试都是一个新的类。

但是，有一个问题:您有所有的测试，那么测试运行人员如何知道它们呢？

这也是我们在这篇文章中试图解决的问题。这些课程需要注册。

看看这段代码:来自 [googletest/.../gt test-internal . h](https://github.com/google/googletest/blob/ea31cb15f0c2ab9f5f5b18e82311eb522989d747/googletest/include/gtest/internal/gtest-internal.h#L1218):

```
// (some parts of the code cut out)
#define GTEST_TEST_(test_case_name, test_name, parent_class, parent_id)\
class GTEST_TEST_CLASS_NAME_(test_case_name, test_name) \
: public parent_class \
{\
  virtual void TestBody();\
  static ::testing::TestInfo* const test_info_ GTEST_ATTRIBUTE_UNUSED_;\
};\
\
::testing::TestInfo* const GTEST_TEST_CLASS_NAME_(test_case_name, test_name)\
  ::test_info_ =\
    ::testing::internal::MakeAndRegisterTestInfo(\
        #test_case_name, #test_name, NULL, NULL, \
        new ::testing::internal::TestFactoryImpl<\
            GTEST_TEST_CLASS_NAME_(test_case_name, test_name)>);\
void GTEST_TEST_CLASS_NAME_(test_case_name, test_name)::TestBody() 
```

Enter fullscreen mode Exit fullscreen mode

我删减了一些代码以使其更短，但基本上`GTEST_TEST_`是在`TEST`宏中使用的，这将扩展到一个新的类。在下半部分，你可能会看到一个名字`MakeAndRegisterTestInfo`。所以这里是班级注册的地方！

注册之后，运行者知道所有现有的测试，并且可以调用它们。

当我为我的一个项目实现一个定制测试框架时，我采用了类似的方法。在我的测试类注册之后，我可以过滤它们，显示它们的信息，当然也可以执行测试套装。

我相信其他测试框架可能会使用类似的技术。

### 灵活性

我之前的例子与未知类型有关:对于测试，你在编译时就知道它们，但是很难在一个方法 create 中列出它们。

尽管如此，这种自注册对于灵活性和可伸缩性还是很有用的。甚至对于我的两个班:`BZCompression`和`ZipCompression`。

现在，当我想添加第三种压缩方法时，我只需编写一个新的类，工厂就会知道这一点——无需干预工厂代码。

好的，好的...我们已经讨论了一些例子，但是你可能想看细节！

所以让我们转到实际的实现。

## 自助注册

我们需要什么？

*   一些接口——我们希望创建从一个接口派生的类。这与“正常的”工厂方法的要求是一样的。
*   工厂类，它也包含可用类型的映射
*   将用于创建给定类的代理。工厂现在不知道如何创建给定的类型，所以我们必须提供一些代理类来做这件事。

对于接口，我们可以使用`ICompressionMethod` :

```
class ICompressionMethod
{
public:
    ICompressionMethod() = default;
    virtual ~ICompressionMethod() = default;

    virtual void Compress() = 0;
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后工厂:

```
class CompressionMethodFactory
{
public:
    using TCreateMethod = unique_ptr<ICompressionMethod>(*)();

public:
    CompressionMethodFactory() = delete;

    static bool Register(const string name, TCreateMethod funcCreate);

    static unique_ptr<ICompressionMethod> Create(const string& name);

private:
    static map<string, TCreateMethod> s_methods;
}; 
```

Enter fullscreen mode Exit fullscreen mode

工厂持有注册类型的映射。这里的要点是，工厂现在使用某种方法(`TCreateMethod`)来创建所需的类型(这是我们的代理)。类型名称和创建方法必须在不同的地方初始化。

此类工厂的实现:

```
map<string, TCreateMethod> CompressionMethodFactory::s_methods;

bool CompressionMethodFactory::Register(const string name, 
                                        TCreateMethod& funcCreate)
{
    if (auto it = s_methods.find(name); it == s_methods.end())
    { // C++17 init-if ^^
        s_methods[name] = funcCreate;
        return true;
    }
    return false;
}

unique_ptr<ICompressionMethod> 
CompressionMethodFactory::Create(const string& name)
{
    if (auto it = s_methods.find(name); it != s_methods.end()) 
        return it->second(); // call the createFunc

    return nullptr;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以从`ICompressionMethod`中实现一个派生类，它将在工厂中注册:

```
class ZipCompression : public ICompressionMethod
{
public:
    virtual void Compress() override;

    static unique_ptr<ICompressionMethod> CreateMethod() { 
        return smake_unique<ZipCompression>();
    }
    static std::string GetFactoryName() { return "ZIP"; }

private:
    static bool s_registered;
}; 
```

Enter fullscreen mode Exit fullscreen mode

自行注册的缺点是一个类的工作量会多一点。如你所见，我们必须定义一个静态的`CreateMethod`。

要注册这样的类，我们所要做的就是定义`s_registered` :

```
bool ZipCompression::s_registered =
  CompressionMethodFactory::Register(ZipCompression::GetFactoryName(),   
                                     ZipCompression::CreateMethod); 
```

Enter fullscreen mode Exit fullscreen mode

这个机制的基本思想是我们依赖静态变量。它们将在调用`main()`之前被初始化。

但是我们能确定所有的代码都被执行了，所有的类都被注册了吗？以后不会在任何地方使用，所以也许可以优化并删除它？初始化的顺序呢？

### 静态 var 初始化

我们可能会遇到两个问题:

#### 静态变量初始化的顺序:

叫做*“静态初始化顺序惨败”*——这是一个静态变量依赖于另一个静态变量的问题。像`static int a = b + 1`(其中`b`也是静态的)。你不能确定`b`会在`a`之前被初始化。请记住，这些变量可能在不同的编译单元中。

幸运的是，对我们来说，这无关紧要。我们可能会在工厂容器中以不同的元素顺序结束，但是每个名称/类型都不依赖于其他已经注册的类型。

但是第一次插入呢？我们能确定地图已经创建并可以使用了吗？

可以肯定的是，我甚至在 SO 问了一个问题:
[C++静态初始化顺序:添加到映射栈溢出](https://stackoverflow.com/questions/48578226/c-static-initialization-order-adding-into-a-map)

我们的地图定义如下:

```
map<string, TCreateMethod> CompressionMethodFactory::s_methods; 
```

Enter fullscreen mode Exit fullscreen mode

这属于[零初始化](http://en.cppreference.com/w/cpp/language/zero_initialization)的范畴。稍后，动态初始化发生了——在我们的例子中，这意味着所有的`s_registered`变量都被初始化了。

看来我们在这里很安全。

你可以在 [isocpp FAQ](https://isocpp.org/wiki/faq/ctors#static-init-order) 和[CP preference-initial ization](http://en.cppreference.com/w/cpp/language/initialization#Non-local_variables)了解更多信息。

#### `s_registered`能被淘汰吗？

幸运的是，我们也很安全:

来自 C++最新草案:[n4713.pdf](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4713.pdf)【basic . STC . static】，第二点:

> 具有静态存储持续时间的变量具有初始化或具有副作用的析构函数；即使它看起来没有被使用，也不应该被消除。

所以编译器不会优化这样的变量。

虽然当我们使用一些模板版本时，这可能会发生...但稍后会详细介绍。

更新:阅读当你的符号来自静态库时会发生什么:[我的最新帖子:静态库中的静态变量初始化，示例](http://www.bfilipek.com/2018/02/static-vars-static-lib.html)

## 分机

总而言之，看起来我们的代码应该是可行的！:)

目前，我只展示了一个基本版本，我们可以考虑一些更新:

### 代理类

在我们的例子中，我只使用了一张保存有`<name, TCreateMethod`的地图——这很有效，因为我们所需要的只是一种创建对象的方法。

我们可以扩展它，使用一个“完整的”代理类作为目标类型的“元”对象。

在我最后的应用程序代码中，我有以下类型:

```
struct CompressionMethodInfo
{
    using TCreateMethod = std::unique_ptr<ICompressionMethod>(*)();
    TCreateMethod m_CreateFunc;
    string m_Description;
}; 
```

Enter fullscreen mode Exit fullscreen mode

除了创建功能，我还添加了`m_Description`。这一增加使得对压缩方法有一个有用的描述。然后，我可以向用户显示所有这些信息，而不需要创建真正的压缩方法。

工厂类现在用的是

```
static map<string, CompressionMethodInfo> s_methods; 
```

Enter fullscreen mode Exit fullscreen mode

在注册类时，我需要传递 info 对象，而不仅仅是创建方法。

```
bool ZipCompression::s_registered =
  CompressionMethodFactory::Register(
      ZipCompression::GetFactoryName(), 
      { ZipCompression::CreateMethod, 
        "Zip compression using deflate approach" 
      }); 
```

Enter fullscreen mode Exit fullscreen mode

### 模板

正如我提到的，自注册的缺点是每个类都需要一些额外的代码。也许我们可以把它打包在某个`RegisterHelper<T>`模板中？

下面是一些代码(只有创建方法，没有完整的信息代理类):

```
template <typename T>
class RegisteredInFactory
{
protected:
    static bool s_bRegistered;
};

template <typename T>
bool RegisteredInFactory<T>::s_bRegistered = 
CompressionMethodFactory::Register(T::GetFactoryName(), T::CreateMethod); 
```

Enter fullscreen mode Exit fullscreen mode

助手模板类包装`s_bRegistered`静态变量，并在工厂中注册它。所以现在，一个你想注册的类只需要提供`T::GetFactoryName`和`T::CreateMethod` :

```
class ZipCompression : public ICompressionMethod, 
                       public RegisteredInFactory<ZipCompression>
{
public:
    virtual void Compress() override { /*s_bRegistered;*/ }

    static unique_ptr<ICompressionMethod> CreateMethod() { ... }
    static std::string GetFactoryName() { return "ZIP"; }
}; 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错...对吗？

但是当你运行它的时候，这个类并没有被注册！

看看[这个代码@coliru](http://coliru.stacked-crooked.com/a/1a203e86a8898a59) 。

但是如果你从`void Compress()`中取消`/*s_bRegistered*/`的注释，注册就可以了。

这是为什么呢？

看起来虽然`s_bRegistered`也是静态变量，但是在模板里面。而模板只有在被使用的时候才会被实例化([见 odr-use @stackoverlow](https://stackoverflow.com/questions/19630570/what-does-it-mean-to-odr-use-something) )。如果该变量在编译器可以移除它的地方没有被使用...

另一个值得单独讨论的话题。

所以总而言之，我们必须更聪明地使用模板助手。我将不得不暂时离开它。

### 不使用字符串命名

我不高兴我们仍然使用字符串来匹配类。

尽管如此，如果小心使用，字符串将发挥巨大作用。也许它们不会超快匹配，但这取决于你的性能需求。理想情况下，我们可以考虑像 int、hashes 或 GUIDs 这样的唯一 id。

### 一些文章的阅读和延伸

*   [ACCU::自我注册类——将多态性发挥到极致](https://accu.org/index.php/journals/597)
*   [在 C++中自注册对象| Dobb 博士的](http://www.drdobbs.com/cpp/self-registering-objects-in-c/184410633)
*   [c++ -如何强制一个静态成员初始化？-堆栈溢出](https://stackoverflow.com/questions/6420985/how-to-force-a-static-member-to-be-initialized?noredirect=1&lq=1)
*   Chromium 注释:静态初始化器
*   静态初始化器会谋杀你的家人——Monica Dinculescu

## 总结

在这篇文章中，我介绍了一种类型注册自己的工厂。这与简单工厂的方式相反，在简单工厂中，所有类型都是预先声明的。

这种方法提供了更多的灵活性，并消除了对工厂支持的类的确切列表的依赖。

缺点是想要注册的类需要请求注册，因此它们需要更多的代码。

让我知道你对自我注册有什么看法？你在你的项目中使用它吗？或者你有更好的方法？

### 行动号召

如果你喜欢我的更多编程故事，就注册我的每周简讯: [Bartek 的编码博客简讯](http://eepurl.com/caCAun)
(你还会得到一些 C++奖金)