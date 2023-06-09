# Go 中扫描字节的计数

> 原文：<https://dev.to/benrcongdon/counting-scanned-bytes-in-go-2lbo>

在最近的一个项目中，我需要从文件的特定块中读取数据。数据是一系列序列化的记录，所以我使用了一个`bufio`扫描仪进行分割。扫描仪很棒，但是它们模糊了读取的确切字节数。在解决这个问题的过程中，我找到了一个非常有效的解决方案。

本质上，问题归结为:我想使用`bufio`的扫描器干净地分割数据记录，但是我也需要精确地控制读取了多少字节。简单地计算输入记录中的字节数是不够的，因为数据记录可能会使用可变宽度分隔符 <sup id="fnref:1">[1](#fn:1)</sup> 拆分。(即`\n`对`\r\n`)。

<figure>[![Problem: Read & Split Records until Chunk Boundary Is Reached](img/d160d9deab795bbffebd8849940515eb.png)](///img/2018-04-10-Counting-Scanned-Bytes-in-Go/chunk_setup.svg) 

<figcaption>问题:读取&拆分记录，直到到达块边界</figcaption>

</figure>

我最初的想法是包装我从中读取记录的`io.Reader`。结果看起来是这样的:

```
type CountingReader struct {
    reader io.Reader
    BytesRead int
}

func (r *CountingReader) Read(p []byte) (n int, err error) {
    n, err = r.reader.Read(p)
    r.BytesRead += n
    return n, err
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法*确实像宣传的那样*工作，因为它计算从读取器读取的字节数。然而，*并没有真正解决我的问题——计算作为记录读取的字节数。比如:* 

```
func main() {
    buf := bytes.NewBuffer([]byte("foo bar baz"))
    reader := &CountingReader{reader: buf}

    scanner := bufio.NewScanner(reader)
    scanner.Split(bufio.ScanWords)
    scanner.Scan()

    fmt.Println(scanner.Text()) // "foo"
    fmt.Println(reader.BytesRead) // 11 :(
} 
```

Enter fullscreen mode Exit fullscreen mode

(你可以在 [Go 游乐场](https://play.golang.org/p/fRgb06S-LlH)试试这个)

我发现，这种行为的原因是`bufio.Scanner`维护着一个[内部缓冲区](https://golang.org/src/bufio/scan.go?s=1184:1841#L20)，它以大块的形式读取这个缓冲区。我的`CountingReader`可以(正确地)说已经从它那里读取了 11 个字节，即使扫描仪只产生了 4 个字节的数据(`"foo "`)。

我的下一个想法是通过包装或重新实现`buffio.Scanner`来实现一个`CountingScanner`来计算处理的字节数。这两种选择都没有吸引力。`io`模块还有 [LimitReader](https://golang.org/pkg/io/#LimitedReader) ，它强制执行读取的最大字节数。对于我的问题，这是行不通的；我的阅读器需要完整地读取文件块中的最后一条记录，即使它跨越了截止点。

<figure>[![io.LimitReader would not allow Logical Splitting](img/185ff40df4cf09180f97517b6e08a423.png)](///img/2018-04-10-Counting-Scanned-Bytes-in-Go/split_diagram.svg) 

<figcaption>木卫一。LimitReader 不允许逻辑拆分</figcaption>

</figure>

最后，我尝试包装扫描仪的 [SplitFunc](https://golang.org/pkg/bufio/#SplitFunc) 。作为参考，SplitFunc 有以下签名:

```
type SplitFunc func(data []byte, atEOF bool) (advance int, token []byte,
    err error) 
```

Enter fullscreen mode Exit fullscreen mode

本质上，`SplitFunc`获取一个字节片，并返回扫描器应该前进多少字节，以及从字节片中提取的“令牌”。

这个`advance`返回值正是我想要的:扫描器真正处理了多少字节。

从这里开始，我构建了一个简单的结构，它包装了一个`SplitFunc`，并在每次执行分割时递增一个内部计数器:

```
type ScanByteCounter struct {
    BytesRead int
}

func (s *ScanByteCounter) Wrap(split bufio.SplitFunc) bufio.SplitFunc {
    return func(data []byte, atEOF bool) (int, []byte, error) {
        adv, tok, err := split(data, atEOF)
        s.BytesRead += adv
        return adv, tok, err
    }
}

func main() {
    data := []byte("foo bar baz")
    counter := ScanByteCounter{}

    scanner := bufio.NewScanner(bytes.NewBuffer(data))
    splitFunc := counter.Wrap(bufio.ScanWords)
    scanner.Split(splitFunc)

    for scanner.Scan() {
        fmt.Printf("Split Text: %s\n", scanner.Text())
        fmt.Printf("Bytes Read: %d\n\n", counter.BytesRead)
    }
    // Split Text: foo
    // Bytes Read: 4

    // Split Text: bar
    // Bytes Read: 8

    // Split Text: baz
    // Bytes Read: 11
} 
```

Enter fullscreen mode Exit fullscreen mode

而且结果成功了！在 [Go 游乐场](https://play.golang.org/p/YOo7BJOhITO)试试这个

以下是一些经验教训:

*   函数包装是扩展标准库功能有用性的好方法
*   注意不要对从/向缓冲区读取多少数据做出假设(特别是当读取器/写入器被其他接口消耗时)

我也很好奇是否有官方的方法来解决这个问题。如果有更干净的解决方案，请告诉我！😄

*封面:[pix abay](https://pixabay.com/en/city-travel-cityscape-aerial-3142651/)T3】*

## 脚注

1。我还想支持任意 SplitFunc 的一般情况，所以任何基于定界符大小的东西都不行。