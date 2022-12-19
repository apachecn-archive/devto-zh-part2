# Go 中的表格驱动测试

> 原文：<https://dev.to/plutov/table-driven-tests-in-go-e7d>

[![Table driven tests in Go](../Images/ff7aff1f7dd7735ce9ae2a61d73e6b9f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wvUMOZQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ez5kpn63di6xdcymvpn8.png)

在 [practice-go](https://github.com/plutov/practice-go) 中，我们经常使用表驱动测试来测试所有的功能场景。例如，`FindAnagrams()`函数返回给我们一个在字典中找到的给定输入的字谜列表。为了能够正确地测试这个函数，我们需要测试多种情况，比如空输入、有效输入、无效输入等等。我们可以修改不同的断言来实现它，但是使用表格测试要容易得多。

假设我们有这个函数:

```
FindAnagrams(string word) []string 
```

Enter fullscreen mode Exit fullscreen mode

下面是我们的表格的样子:

```
var tests = []struct {
    name string
    word string
    want []string
}{
    {"empty input string", "", []string{}},
    {"two anagrams", "Protectionism", []string{"Cite no imports", "Nice to imports"}},
    {"input with space", "Real fun", []string{"funeral"}},
} 
```

Enter fullscreen mode Exit fullscreen mode

通常表是匿名结构的一部分，但是你可以先定义结构或者使用一个现有的结构。我们还有一个描述特定测试用例的`name`属性。

在我们有了一个表之后，我们可以简单地迭代它并做一个断言:

```
func TestFindAnagrams(t *testing.T) {
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := FindAnagrams(tt.word)
            if !reflect.DeepEqual(got, tt.want) {
                t.Errorf("FindAnagrams(%s) got %v, want %v", tt.word, got, tt.want)
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用另一个函数代替`t.Errorf()`，`t.Errorf()`只是记录错误，测试继续。

[验证](https://github.com/stretchr/testify)包是非常流行的 Go 断言包，用来使单元测试清晰明了，例如:

```
assert.Equal(t, got, tt.want, "they should be equal") 
```

Enter fullscreen mode Exit fullscreen mode

`t.Run()`将启动一个子测试，如果您以详细模式(`go test -v`)运行测试，您将看到每个子测试的结果:

```
=== RUN   TestFindAnagrams
=== RUN   TestFindAnagrams/empty_input_string
=== RUN   TestFindAnagrams/two_anagrams
=== RUN   TestFindAnagrams/input_with_space 
```

Enter fullscreen mode Exit fullscreen mode

因为 Go 1.7 测试包能够通过使用`(*testing.T).Parallel()`并行化子测试。请确保并行化您的测试是有意义的！

```
t.Run(tt.name, func(subtest *testing.T) {
    subtest.Parallel()
    got := FindAnagrams(tt.word)
    // assertion
}) 
```

Enter fullscreen mode Exit fullscreen mode

就这样，享受在 Go 中编写表格驱动测试吧！