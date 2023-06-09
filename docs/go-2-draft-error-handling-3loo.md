# Go 2 草稿:错误处理

> 原文：<https://dev.to/dean/go-2-draft-error-handling-3loo>

# Go 2 正在起草中。

如果你还没有听说过，那么`Go 2`将会有一些重大的语言变化。Go 团队最近提交了这些变化的[草案](https://go.googlesource.com/proposal/+/master/design/go2draft.md)，作为 3 部分系列的一部分，我想浏览每个部分，解释它，并陈述我对它们的看法！这是这个系列的第一部分。在错误处理之后，我们将讨论错误值，然后我们就可以开始大讨论了...仿制药！

请记住，这些只是草稿。它们很可能会被添加到语言中，但也有可能不会。当然，请记住，这些草稿并不是最终版本。语法可能会和现在有点不同。

这些可能不是语言的唯一变化，但可能是唯一的主要变化。

# 那么，错误处理有什么大不了的？

目前，如果你想检查一个错误，你需要做`if err != nil { ...`。这个构造很好，但是当您处理任何类型的 I/O 时，您的代码看起来就像这样...(使用来自[草案](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)的例子)

```
// From the draft
func CopyFile(src, dst string) error {
    r, err := os.Open(src)
    if err != nil {
        return fmt.Errorf("copy %s %s: %v", src, dst, err)
    }
    defer r.Close()

    w, err := os.Create(dst)
    if err != nil {
        return fmt.Errorf("copy %s %s: %v", src, dst, err)
    }

    if _, err := io.Copy(w, r); err != nil {
        w.Close()
        os.Remove(dst)
        return fmt.Errorf("copy %s %s: %v", src, dst, err)
    }

    if err := w.Close(); err != nil {
        os.Remove(dst)
        return fmt.Errorf("copy %s %s: %v", src, dst, err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须写三遍`fmt.Errorf("copy %s %s: %v", src, dst, err)`，两遍`w.Close()`和`os.Remove(dst)`...这是一个非常小的函数。那么我们该如何解决这个问题呢？

# 介绍:`check`和`handle`！

`check`和`handle`将是新的关键字，你可以把它们想象成一个`panic-defer-recover`，但是只在一个函数中使用。

`check`的格式是`check <expression>`，其中`<expression>`要么是错误，要么是函数调用，最后一个返回值是错误。这些也是表达式本身，如果错误是`nil`，那么它返回所有其他参数(例如:`f := check os.Open(fileName)`)。

如果误差值不为零，那么`check`将跳转到`handle`模块。为了理解我的意思，让我们看一个简单的例子。

Go 1:

```
type Parsed struct { ... }

func ParseJson(name string) (Parsed, error) {

    // Open the file
    f, err := os.Open(name)
    if err != nil {
        return fmt.Errorf("parsing json: %s %v", name, err)
    }
    defer f.Close()

    // Parse json into p
    var p Parsed
    err = json.NewDecoder(f).Decode(&p)
    if err != nil {
        return fmt.Errorf("parsing json: %s %v", name, err)
    }

    return p
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那么让我们用`check`和`handle`来简化这个！

```
type Parsed struct { ... }

func ParseJson(name string) (Parsed, error) {
    handle err {
        return fmt.Errorf("parsing json: %s %v", name, err)
    }

    // Open the file
    f := check os.Open(name)
    defer f.Close()

    // Parse json into p
    var p Parsed
    check json.NewDecoder(f).Decode(&p)

    return p
} 
```

Enter fullscreen mode Exit fullscreen mode

怎么样？我们已经削减了很多样板文件！

但是之前的那个例子呢...它有更多的错误处理！如果文件复印不正确，我们就必须清理...那么我们该如何处理呢？再来看看[选秀](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)！

```
// From the draft
func CopyFile(src, dst string) error {
    handle err {
        return fmt.Errorf("copy %s %s: %v", src, dst, err)
    }

    r := check os.Open(src)
    defer r.Close()

    w := check os.Create(dst)
    handle err {
        w.Close()
        os.Remove(dst) // (only if a check fails)
    }

    check io.Copy(w, r)
    check w.Close()
    return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你有多个`handle`块，它们将从最底层到最顶层执行。这允许您在函数流中出现错误时进行更多的清理。

当然，这个构造也适用于不返回错误的函数，正如在[草案](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)中提到的！

```
// From the draft
func main() {
    handle err {
        log.Fatal(err)
    }

    hex := check ioutil.ReadAll(os.Stdin)
    data := check parseHexdump(string(hex))
    os.Stdout.Write(data)
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我怎么看？

我觉得这是一个真的很好的改变！我唯一的真正问题是现在`check`和`handle`变成了关键字，它们都是变量名，而不是*不常见的*。我曾经喜欢过`collect`的想法(正如我在[之前的博文](https://dev.to/deanveloper/my-favorite-go-2-proposals-3lie)中所展示的那样)，尽管我认为在我写完之后，我对`collect`的看法发生了变化。它看起来就像`try-catch`，没有非常直观的语法。

`check-handle`还是蛮有点像`try-catch`的，虽然强大很多。它允许随着函数的进展进行更多的清理，而不增加更多的缩进。每个错误检查也是显式的，所以你很快就知道函数的出口点在哪里。

[草稿](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md)也包含了很多关于是什么激发了这个决定的细节！他们从 Rust 和 Swift 那里获得了很多灵感，它们都有必须明确检查的错误(不像 Java、C++、Python 等。其具有栈的隐式展开)。

这一改变也适用于[新的`error`习语](https://go.googlesource.com/proposal/+/master/design/go2draft-error-values-overview.md)...但那是为系列的下一篇文章准备的！