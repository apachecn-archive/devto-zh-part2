# Go 2 草稿:错误值

> 原文：<https://dev.to/dean/go-2-draft-error-values-lid>

# Go 2 正在起草中。

如果你还没有听说过，那么`Go 2`将会有一些重大的语言变化。Go 团队最近提交了这些变化的[草案](https://go.googlesource.com/proposal/+/master/design/go2draft.md)，作为 3 部分系列的一部分，我已经仔细阅读了每个部分，解释了它，并陈述了我对它们的看法！这是该系列的第二部分。上次我做了[错误处理](https://dev.to/deanveloper/go-2-draft-error-handling-3loo)，这次我将讨论错误值！

请记住，这些只是草稿。它们很可能会被添加到语言中，但也有可能不会。当然，请记住，这些草稿并不是最终版本。语法可能会和现在有点不同。

这些可能不是语言的唯一变化，但可能是唯一的主要变化。

# 我以为你已经讨论过错误了！

好的，第一部分是关于错误*处理*，以及一个新的语法来帮助处理错误。这部分是关于对`error`接口和`errors`包的改进。可能会有一些非常好的改进！

# 好吧，那么错误包有什么问题呢？

有两个主要问题:检查错误和格式化错误。

## 检查错误

目前，如果没有自定义实现，就无法获取错误的上下文。因此，没有提供方法来为您返回的错误提供上下文，或者查看您收到的错误是否有原因。

为了解决这个问题，可以在`errors`包中定义以下内容:

```
package errors

// Describes an error which wraps another error
type Wrapper interface {
    Unwrap() error
}

// Reports whether err or any of the errors in its chain is equal to target.
function Is(err, target error) bool

// Checks whether err or any of the errors in its chain is a value of type E.
// If so, it returns the discovered value of type E, with ok set to true.
// If not, it returns the zero value of type E, with ok set to false.
func As(type E)(err error) (e E, ok bool)

// NOTE this uses the syntax from the generics draft:
// https://go.googlesource.com/proposal/+/master/design/go2draft-generics-overview.md 
```

Enter fullscreen mode Exit fullscreen mode

`Wrapper`是描述一个错误的接口，该错误包装了另一个错误。这形成了一个错误的单链表(或称“链”)，头部是最常见的错误(“无法初始化配置”)，尾部是错误的根本原因(“找不到文件”)。

`Is`相当于旧的`if err == pack.ErrSomething`，但是它检查整个错误链。这样，你可以做`if errors.Is(err, os.ErrNotExist)`，它会告诉你*在任何时候*错误是由一个不存在的文件引起的。

`As`相当于旧的`smth, ok := err.(pack.ErrSomething)`，但是它也检查整个错误链。请注意，`As`函数使用了泛型，我将在下一篇博文中介绍。

无论如何，`As`的用法看起来应该和`errne, ok := errors.As(*os.ErrNotExist)(err)`差不多。我个人不喜欢泛型的双括号语法(老实说，我一般不太喜欢泛型...)

如果语言中还没有泛型，那么一个好的替代品就是

```
var errne *os.ErrNotExist
errors.AsValue(&errne, err) 
```

Enter fullscreen mode Exit fullscreen mode

## 格式错误

还有，查看打印错误时，信息往往很少！打印错误会给出类似“无法读取配置”或“无效路径”的信息，并且没有获得更多详细信息的好方法。

为了解决这个问题，可以在`errors`包中添加以下内容:

```
type Formatter interface {
    Format(p Printer) (next error)
} 
```

Enter fullscreen mode Exit fullscreen mode

`Formatter`是描述可能被格式化的错误的接口。`Printer`是一个包含`Print`和`Printf`功能以及`Detail`功能的接口。`Printer`提供打印错误的包(如果通过 fmt 打印，通常是 fmt)。打印...()).

然后，`fmt.Errorf()`可以被改进以返回一个实现`Formatter`的错误。还建议，如果格式字符串以`: %v`或`: %s`结尾，它也应该返回一个`Wrapper`，从而进一步改善返回的错误。

自定义格式的错误可能如下:

```
// Implements error and errors.Formatter
type ConfigError struct {
    File     string       // A human-readable name for this config
    Source   string       // The source file and line this error occurred on
    CausedBy error        // The error this was caused by
}

func (e *ConfigError) Init(file string, cause error) {
    e.File = file

    if _, file, line, ok := runtime.Caller(1); ok {
        e.Source = fmt.Sprintf("%s:%s", )
    } else {
        e.Source = "error source unavailable"
    }

    e.CausedBy = cause
}

func (e *ConfigError) Format(p errors.Printer) (next error) {
    p.Printf("error reading config %s", e.File)
    if p.Detail() { // if printing with `%+v` rather than just `%v`
        p.Printf("Perhaps incorrect file name or invalid format?")
        p.Print(e.Source)
    }
    return e.CausedBy
}

func (e *ConfigError) Error() string {
    return fmt.Sprint(e) // `fmt` will call the new Format function
} 
```

Enter fullscreen mode Exit fullscreen mode

当它被打印出来的时候，它会是这样的...

```
fmt.Printf("%v", err)

error reading config MyConfig

===

fmt.Printf("%+v", err)

error reading config MyConfig:
    Perhaps incorrect file name or invalid format?
    github.com/deanveloper/config:1337
-------- file does not exist 
```

Enter fullscreen mode Exit fullscreen mode

俏皮！

# 把这两者放在一起

现在，让我们给我们的`ConfigError`再添加一个函数来完成它。让我们添加我们的`Unwrap`函数，这样我们就可以使用`errors.Is`和`errors.As`了！

```
// Implements error, errors.Wrapper, and errors.Formatter
type ConfigError struct {
    File     string       // A human-readable name for this config
    Source   string       // The source file and line this error occurred on
    CausedBy error        // The error this was caused by
}

func (e *ConfigError) Init(file string, cause error) {
    e.File = file

    if _, file, line, ok := runtime.Caller(1); ok {
        e.Source = fmt.Sprintf("%s:%s", )
    } else {
        e.Source = "error source unavailable"
    }

    e.CausedBy = cause
}

func (e *ConfigError) Format(p errors.Printer) (next error) {
    p.Printf("error reading config %s", e.File)
    if p.Detail() { // if printing with `%+v` rather than just `%v`
        p.Printf("Perhaps incorrect file name or invalid format?")
        p.Print(e.Source)
    }
    return e.CausedBy
}

func (e *ConfigError) Unwrap() error {
    return e.CausedBy
}

func (e *ConfigError) Error() string {
    return fmt.Sprint(e) // `fmt` will call the new Format function
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用这个错误(注意，我使用的是“错误处理”Go 2 提案中的语法。我已经写了一篇关于这个的博文

```
 func main() {
    handle err {
        if cfgErr, ok := errors.As(*config.ConfigError)(err); ok {
            if errors.Is(err, os.ErrNotFound) {
                // Nice, clean output if we couldn't find the file
                log.Fatalln("Could not find config file %s", cfgErr.File)
            }
        }
        // Hmm, let's get a more verbose output.
        // Maybe a parsing error?
        log.Fatalln("%+v", err)
    }

    check config.Create("MyConfig")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我的建议

就`errors.Is`而言，我真的想要一种检查多个错误的方法。

也许我们可以用`errors.IsAny(root error, causes ...error) error`代替`errors.Is(root, cause error) bool`(名字很糟糕，但是语义很重要)，然后我们可以在上面使用`switch err`。没有一种有效的方法来检查多种类型而不进行循环，这是非常令人沮丧的。

另外，我认为这增加了误差值的复杂性。对我来说，围棋的一个特别之处是错误很简单。我喜欢`Wrapper`的想法，还有`errors.Is`和`errors.As`，但是`Formatter`我觉得有点多。也许给`errors`添加一个`type Detailer interface { Detail() string }`，让`fmt`负责实际的格式化，因为几乎所有的`Format`函数都非常相似...

```
func (e *MyErr) Format(p errors.Printer) (next error) {
    p.Print(/* short error string */)
    if p.Detail() {
        p.Print(/* details */)
    }
    return /* caused by */
} 
```

Enter fullscreen mode Exit fullscreen mode

等等，我写的占位符可能只是函数！

如果 Go 只有一个`errors.Detailer`接口，那么我们的`ConfigError`应该是这样的...

```
// Implements error, errors.Wrapper, and errors.Detailer
type ConfigError struct {
    File     string       // A human-readable name for this config
    Source   string       // The source file and line this error occurred on
    CausedBy error        // The error this was caused by
}

func (e *ConfigError) Init(file string, cause error) {
    e.File = file

    if _, file, line, ok := runtime.Caller(1); ok {
        e.Source = fmt.Sprintf("%s:%s", )
    } else {
        e.Source = "error source unavailable"
    }

    e.CausedBy = cause
}

func (e *ConfigError) Detail() string {
    return "Perhaps incorrect file name or invalid format?\n" + e.Source
}

func (e *ConfigError) Unwrap() error {
    return e.CausedBy
}

func (e *ConfigError) Error() string {
    return "error reading config " + e.File
} 
```

Enter fullscreen mode Exit fullscreen mode

然后`fmt`将能够打印出链中的每个错误。

# 总之...

我认为这些是受欢迎的变化，我的变化有点吹毛求疵。我对`Format`函数很满意，尽管我认为仅仅增加一个`Detailer`接口会更好。我会向 Go 团队提出这个建议。