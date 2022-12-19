# 我如何在 Leaf 编译器中使用“variant”

> 原文：<https://dev.to/mortoray/how-i-use-variant-in-the-leaf-compiler-51n3>

变量是可以存储不同类型值的数据类型，与固定类型相反。与一般的`object`或`untyped`变量相比，可存储的类型数量有限。我在我的 [Leaf](http://leaflang.org/) 编译器中的两个关键地方使用了它们:日志和类型信息。

> 我仍然使用 boost::variant 而不是 std::variant。我正在等待 C++17 编译器的广泛部署，然后再升级(比如与新的长期 Ubuntu 版本一起)。

## 测井

我最近给 Leaf 添加了一个新的日志系统。在创建日志条目时，调用者提供错误引用和上下文数据。`variant`用于捕捉不同类型的上下文信息。我以前在我的[低延迟日志记录系统](https://mortoray.com/2014/05/29/wait-free-queueing-and-ultra-low-latency-logging/)中就这样使用过`variant`。

```
logger.error( "cerr-unknown-identifier", logger::item_symbol( symbol.name ) ); 
```

Enter fullscreen mode Exit fullscreen mode

`cerr-unknown-identifier`是错误消息标识符。这是存储在 YAML 文件中的一个错误消息的密钥，这个特定的错误是:

```
cerr-unknown-identifier:
    text: Unknown identifier `{symbol}` 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的占位符`{symbol}`。不知何故，记录者需要用一个符号来替换它。这就是`logger::item_symbol( symbol.name )`参数所提供的。

但是其他类型呢？例如，参数不匹配调用涉及更多信息:

```
mismatch-argcount:
    text: The function `{symbol}` expects {expect} argument(s), you provided {actual}. 
```

Enter fullscreen mode Exit fullscreen mode

调用者传递给`error`的参数比以前更多。

```
logger.error( 'mismatch-argcount', logger::item_symbol( symbol.name ), 
    logger::item_expect( func_arg_count ), logger::item_actual( call_args.size() ) ); 
```

Enter fullscreen mode Exit fullscreen mode

不同的项目携带不同类型的信息。该函数调用掩码项构造，但是这些`item_`函数中的每一个都返回一个`logger::item`。

```
typedef boost::variant<
        std::string,
        int64_t,
        source_location> item_variant_t;
struct item {
    item_type_t type;
    item_variant_t value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

`type`带有语义，如`i_symbol`或`i_actual`。`value`包含与该项目相关的数据。这是一种允许使用`string`、`int64_t`或`source_location`的`variant`类型。

> `error`函数有一些变化，接受项目列表和单个项目。宏最常用于进行这些调用，因为它为行和源信息添加项目。

## 类型特质

Leaf 的类型系统有两个并行的系统:具体类型和类型说明符。类型说明符允许不完整的类型和类型约束。我们在叶源代码中看到说明符。

```
var pine : optional integer 32bit
var cone : optional = 25
var twig : float high = 1/2 
```

Enter fullscreen mode Exit fullscreen mode

`optional integer 32bit`包含三个部分，存储在一个`type_spec::part`结构中。`type_spec`不太了解这些部件的含义，只知道如何存储它们。它有一个`std::vector<part>`零件列表。

由于每个部分的值类型变化很大，我使用一个变量来存储所有的可能性。

```
struct part {
    part_type_t type;
    boost::variant<
        bool,
        extr_type,
        extr_type::reference_t,
        int,
        intr_type_compat::type_t,
        intr_type::fun_class_t,
        intr_type_function::access_t,
        intr_type_tuple::pack_t,
        shared_ptr<intr_type const>, //MUST not be an instance!
        std::string,
        type_spec_symbol,
        intr_type_function::convention_t
    > value;
    //sub-parametrics
    std::vector<shared_ptr<type_spec const>> sub;
    //attached expression
    shared_ptr<node const> node_expr;
    shared_ptr<expression const> expr;
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为使用`variant`会很麻烦，而且想要更强的输入，所以`type_spec`的用户不直接使用变体。所有的访问都要通过模板函数。

```
// creating the `optional integer 32bit` specifier
type_spec ts.
ts.set<pt_data_bitsize>( 32 );
ts.set<pt_optional>( true );
ts.set<pt_fun_class>( intr_type::integer ); 
```

Enter fullscreen mode Exit fullscreen mode

`set`调用防止将错误类型的信息与零件相关联。它有这样的签名:

```
template<part_type_t PT>
part & set( typename part_type_descriptor<PT>::type value ) 
```

Enter fullscreen mode Exit fullscreen mode

我用专门的模板将一个枚举值映射到类型信息。设置隐藏在宏的后面，但是这里有一个`pt_optional`的例子:

```
template<> struct part_type_descriptor<pt_optional> {
    typedef bool type;
}; 
```

Enter fullscreen mode Exit fullscreen mode

有一个匹配的`if_get`函数，它返回特定部分的值，如果它存在的话。这种方法增加了强类型，并隐藏了在`type_spec`类中使用`variant`的复杂性。

```
template<part_type_t PT>
boost::optional<typename part_type_descriptor<PT>::type> if_get() const { 
```

Enter fullscreen mode Exit fullscreen mode

> 我喜欢 C++将枚举值映射到具体类型的能力。它和 variant 一起，是在保存可变类型信息时使`type_spec`类型安全的关键。这种模板灵活性是 C++吸引我的地方之一。

## 你呢？

让我知道你如何在代码中使用`variant`？即使不是 C++，这个概念在其他语言中也是存在的。或者，告诉我你想如何使用`variant`，但是只有一个通用的对象类型。