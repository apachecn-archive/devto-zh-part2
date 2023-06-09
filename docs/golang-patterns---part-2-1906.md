# Golang 模式-第二部分

> 原文：<https://dev.to/napicella/golang-patterns---part-2-1906>

大家好！这是关于常见 go lang 模式的文章的第二部分。
你可以在这里找到第一部[。](https://dev.to/napicellatwit/golang-patterns-5a64)

## 一衬 if 语句

```
if err := someOperation(); err != nil {
    // error handling here
} 
```

Enter fullscreen mode Exit fullscreen mode

我个人并不经常使用它，但是如果你想做的只是向上游返回错误，它可以节省几次击键的时间。

```
if err := someOperation(); err != nil {
    return errors.Wrap(err, "Ops, unable to complete some operation")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 行者

在我偶然发现的所有代码库中，我已经看到了这种用法。
最值得注意的是，它被用在[路径/文件路径包](https://golang.org/pkg/path/filepath/#Walk)中。

这种需求类似于迭代器模式所解决的需求:

*   将算法与数据结构实现分离
*   将迭代数据所需的逻辑与作用于数据的逻辑分离开来

```
func WalkInWordGrams(walker func(wordgram *wordgrams.WordGram) error) error {
   // Iteration happens here
}

WalkInWordGrams(func(wordGram *wordgrams.WordGram) error {
        for _, stats := range wordGram.Stats() {
            // ... do something meaningful with stats
        }

        return nil
    }) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们调用了一个函数`WalkInWordGrams`，它将处理数据的参数λ作为参数。
为数据结构中的每个元素调用 lambda func。
如果出现不可恢复的错误，我们可以通过返回它来停止迭代。

使用前一篇文章中的知识，我们可以通过添加 lamdbda 函数的类型来改进 DSL。

```
type WordgramWalker func(wordgram *wordgrams.WordGram) error

func WalkInWordGrams(walker WordgramWalker) error {
   // Iteration happens here
}

WalkInWordGrams(func(wordGram *wordgrams.WordGram) error {
        for _, stats := range wordGram.Stats() {
            // ... do something meaningful with stats
        }

        return nil
    }) 
```

Enter fullscreen mode Exit fullscreen mode

## 测试数据

这在编写测试时非常有用。
Go build 忽略名为`testdata`的目录，当它运行测试时，它将当前目录设置为包目录。这允许你使用相对路径 testdata 目录作为加载和存储数据的地方。
例如我们可以写一个如下的效用函数:

```
func LoadTestFile(name string, failureHandler func(message string)) []byte {
    path := filepath.Join("testdata", name)
    bytes, e := ioutil.ReadFile(path)
    if e != nil {
        failureHandler(e.Error())
    }

    return bytes
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数从 testdata 中读取文件，并将其返回给调用者。如果失败，它调用失败处理程序。
我们可以这样用:

```
import (
    "bytes"
    . "github.com/onsi/ginkgo"
    . "github.com/onsi/gomega"
    "io/ioutil"
    "net/http"
    "path/filepath"
    "testing"
)

func Test(t *testing.T) {
    RegisterFailHandler(Fail)
    RunSpecs(t, "Test suite")
}

var _ = Describe("Some test which requires a file", func() {
    It("does what is supposed to do", func() {
        image := LoadTestFile("amalfi-coast.jpg", Fail)
                ... 
```

Enter fullscreen mode Exit fullscreen mode

## Godoc 示例

这很酷，有助于改进我们代码的文档。
来自 golang 文档:

> Godoc 示例是 Go 代码的片段，显示为包文档，并通过运行它们作为测试来验证。它们也可以通过用户访问 godoc 网页并点击相关的“运行”按钮来运行。

写一个真的很简单。我们需要在一个测试文件中编写一个名称以`Example`开头的函数。

```
func ExampleChain() {
    endpoint, _ := chain(
        loadEndpointFromConfigFile,
        loadEndpointFromEnvVariables,
        loadEndpointFromDatabase,
    ).get()

    fmt.Println(endpoint)
    // Output: some-endpoint
}

func loadEndpointFromEnvVariables() (string, error) {
    return "", nil
}

func loadEndpointFromConfigFile() (string, error) {
    return "", nil
}

func loadEndpointFromDatabase() (string, error) {
    return "some-endpoint", nil
} 
```

Enter fullscreen mode Exit fullscreen mode

最酷的是这个例子变成了一个测试。
`Output comment`用于验证输出是否符合我们的预期。
它通过捕获写入标准输出的数据，然后将输出与示例的“output:”注释进行比较来实现这一点。如果测试的输出与其输出注释匹配，则测试通过。

## 结论

感谢阅读！如果你认为我遗漏了一些有趣的模式，请告诉我！