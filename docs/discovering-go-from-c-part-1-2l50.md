# 从 C++中发现 Go[第 1 部分]变量、条件、循环、打印。

> 原文：<https://dev.to/pmalhaire/discovering-go-from-c-part-1-2l50>

[![Go](img/d2f46693f191a38a5a50450b2912f8f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---9vRRTIJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uewk2k58y24qgzmskrur.jpeg)
[![C++](img/7147f692f9db89985847b004be86263d.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gc56hoi_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hcoaiipwkhf6fgwud3sj.png)

# 变量初始化和声明

Go 和 C++在这里走的是同一条轨道。变量的初始化和声明非常相似。

```
var test int
test = 1

var test int = 1

test := 1 
```

Enter fullscreen mode Exit fullscreen mode

更多约束在这里不容易出错，但编写起来更痛苦。
一个典型的误差是这样的变量阴影:

```
import (
    "fmt"
)

func main() {
    variable := 1
    if true {
        variable := 3
        fmt.Println(variable)
    }
    fmt.Println(variable)
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://play.golang.org/p/I3IO9Tyn17G)

与`=`相比，`auto a = x`比`:=`更容易被发现。g++警告是一种可能性。从我的经验来看，写 C++比写 Go 更少出现阴影。

```
#include <iostream> 
using namespace std;

int main()
{
    auto variable = 1;

    {
        auto variable = 3;
        cout << variable << endl;
    }
    cout << variable << endl;
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://onlinegdb.com/HJptPFDoM)

Go way 看起来不错，但味道很奇怪。

# 未使用的变量

```
func main() {
    test := 1
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://play.golang.org/p/x0wLIWdDQba)

```
 prog.go:5:2 test declared and not used 
```

Enter fullscreen mode Exit fullscreen mode

我有时在这方面很懒，而且我不是唯一一个。对于代码的可维护性来说，这简直太棒了。

这对于调试来说可能很烦人。如果你删除了一些表达式，Go 开始抱怨未使用的变量，然后抱怨未使用的导入，等等。

```
 import "fmt"

func main(){
   test := "test"
/*
   fmt.Println(test)
*/
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 prog.go:X:5: fmt imported and not used 
```

Enter fullscreen mode Exit fullscreen mode

避免这种情况最简单的方法是用 if (false) {…}包装 expression，但这很麻烦。

Clang 和 GCC 对长期以来未使用的变量发出警告。我想有 GCC/Clang 风格(发布警告和-Werror)。

我喜欢这样，因为它让我的代码更干净。一些改进尤其有助于编写调试代码。

# 多个返回值

我在 Lua 中发现了这一点，并发现它非常有趣。

这里有一个例子:

```
package main
import "fmt"

func set() (x, y, z int) {
    return 1, 2, 3
}

func main() {
    x, y, z := set()
    fmt.Println(x, y, z)
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://play.golang.org/p/cc1nZdtuNhw)

```
1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:当一个函数返回多个变量，而不是所有的变量都被使用时，避免使用需要很长时间计算的结果是很重要的。
我希望我能添加一个编译警告，当我忽略了需要 cpu 时间来计算的变量时会发出这个警告。

C++实现这一点的方法:

```
#include <iostream>
#include <tuple> 
using namespace std;

auto set() {
    return make_tuple(1,2,3);
}

int main()
{
    auto [a, b, c] = set();

    std::cout << a << " " << b << " " << c << std::endl;

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://wandbox.org/permlink/4OoMu4NEBo2LGZWQ)

我绝对喜欢这个。这是压缩代码的最好方式。

# 条件如果，否则如果

Go 只强制一种语法:

```
if A {
} else {
} 
```

Enter fullscreen mode Exit fullscreen mode

不允许在 C++风格中嵌套`else if`:

```
if A {
    If B {
    } else if C {
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

起初 go 是摧毁我的大脑来自 C++。

感觉是这样的:

*   尝试原始代码
*   阅读文档
*   密码
*   生气
*   阅读文档
*   密码
*   抓住要点
*   去吧。

强制使用一种语法是避免混合使用多种语法带来的错误的一种方式。

一种口述，但为什么不呢(在这种情况下，只有这种情况下)。

即使我不喜欢失去自由，阅读总是以同样方式编写的代码也更容易。

# 为循环

我发现简单的第一件事是，我们试图取代它，但我们不必重新发明轮子。For 也被扩展了，这很好，但是 C 奶奶不会被弄糊涂。

```
for i := 1; i<=4; i++ {
        fmt.Println(i)
}

numbers := []int{1,2,3,4}
for _,v := range numbers {
    fmt.Println(v)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么新东西。C++有很多模式可以做到这一点，包括还没有在 Go 中实现的迭代器。

# 开关

Switch in go，易读不远 C++。

**去**T2】

```
switch t {
case 1:
    fmt.Println("case 1")
default:
    fmt.Println("default")
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://play.golang.org/p/Jc0bbJoOLq-)

**c++**T2】

```
switch (var) {
        case 1:
            cout << "case 1" << endl;
            break;
        default:
            cout << "default" << endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://onlinegdb.com/ByTFUuwjG)

# 字符串打印

多种方式做非常接近的事情，我很快就习惯了，但觉得很烦。

```
import "fmt"

func main() {
    str := "hello"
    slice := []string{"one", "two", "three"}

    fmt.Print(str)
    fmt.Print("\n")
    fmt.Print(slice)
    fmt.Print("\n")

    fmt.Println(str)
    fmt.Println(slice)

    fmt.Printf("%v\n", str)
    fmt.Printf("%v\n", slice)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
hello
[one two three]
hello
[one two three]
hello
[one two three] 
```

Enter fullscreen mode Exit fullscreen mode

这真的会变得很混乱，而`Println("string")`函数是一个可以被`Print("string\n")`替换的钩子。注意 Println 在所有平台上的行为都是一样的，它不会在 Windows 中将分隔符更改为`\r\n`。这只是句法糖为什么不。

比 Cpp 更简单的是打印切片内容。

Cpp 会做:

```
#include <iostream> 
using namespace std;

int main()
{
    auto list = {1, 2, 3};

    for ( const auto &v: list) {
        cout << v << " ";
    }
    cout << endl;

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://onlinegdb.com/HyEA__Pif)

使用 Print 和 Println 功能更长时间后，生活变得更加轻松。Go 经常被用在各种容器中，用一种简单紧凑的方式来打印对象和值是很好的。

# 什么是 println？

我只是把`fmt.Println`输错成了`println` :

```
import "fmt"

func main() {

    fmt.Println("Hello")
        // ok 
    println("hello")
        // ok 

    slice := []string{"toto", "tutu"}
    fmt.Println(slice)
        // ok
    println(slice)
        // ko
} 
```

Enter fullscreen mode Exit fullscreen mode

```
hello
hello
[toto tutu]
[2/2]0x1040a0d0 
```

Enter fullscreen mode Exit fullscreen mode

有趣的事情，但到底是什么，我猜这是语言内部？实际上它只是一个没有额外输入的调试助手，将来可能会消失。

这只是一件奇怪的事情，没有真正的影响(只是不要提交 println)。

更多详情[此处](https://stackoverflow.com/questions/14680255/whats-the-difference-between-fmt-println-and-println-in-go)。

# Bytes vs Rune

符文是 unicode 字符。

```
import "fmt"
import "reflect"

func main() {

str := "s"
str_utf8 := "漢"
rune_utf8 := []rune("漢")

//printing the object the first item and the first item type for each case
fmt.Println(str, string(str[0]), reflect.TypeOf(str[0]))
fmt.Println(str_utf8 , string(str_utf8[0]), reflect.TypeOf(str_utf8[0]))
fmt.Println(rune_utf8 , string(rune_utf8[0]), reflect.TypeOf(rune_utf8[0]))
} 
```

Enter fullscreen mode Exit fullscreen mode

```
s s uint8
漢 æ uint8
[28450] 漢 int32 
```

Enter fullscreen mode Exit fullscreen mode

[测试代码](https://play.golang.org/p/b8aiNwdkhpS)

有符文是好事，但为什么不把它设为默认。大部分时间字符串都是要显示的。那么 Unicode 就有意义了。我觉得停在中间是个好的开始。

# 结论

Go 今天发展很快，它的语法很容易掌握。基本上，它有一些好东西。

下一部分将讨论来自 C++的 Go 中的对象和并发性/并行性，Go 和 C++的差异有更大的影响。

感谢 Dmytro 看了我的帖子。