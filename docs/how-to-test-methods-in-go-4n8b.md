# 如何在 Go 中测试方法

> 原文：<https://dev.to/radlinskii/how-to-test-methods-in-go-4n8b>

你好，上周我开始学习戈朗。在 Go 的[之旅之后，我实现了一个函数来计算给定 float64 数的平方根。](https://tour.golang.org/methods/20) 

```
import "fmt"

type ErrNegativeSqrt float64

func (e ErrNegativeSqrt) Error() string {
    return fmt.Sprintf("cannot Sqrt negative number: %g\n", float64(e))
}

// Sqrt calculates the square root of a number.
// If given negative number it returns an error.
func Sqrt(x float64) (float64, error) {
    if x < 0 {
        return 0, ErrNegativeSqrt(x)
    } else {
        z := float64(x)
        for i := 0; i < 100; i++ {
            z -= (z*z - x) / (2 * z)
        }
        return z, nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试它是否像预期的那样工作，我写了这个测试:

```
 import (
    "math"
    "testing"
)

func TestSqrt(t *testing.T) {
    var tests = map[float64]float64{
        3:  math.Sqrt(3),
        2:  math.Sqrt(2),
        1:  math.Sqrt(1),
        0:  math.Sqrt(0),
        4:  math.Sqrt(4),
        5:  math.Sqrt(5),
        -5: 0,
        -1: 0,
    }
    precision := 0.00000001

    for key, expectedVal := range tests {
        val, _ := Sqrt(key)
        if val < expectedVal-precision || val > expectedVal+precision {
            t.Error(
                "For", key,
                "expected", expectedVal,
                "got", val,
            )
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我的问题是*如何为我编写的`ErrNegativeSqrt`类型*的`Error`方法编写一个测试，以便`ErrNegativeSqrt`可以实现`error`接口？

提前感谢。❤️

### PS。这是我第一次发帖子！🙌

PPS。请随意检查我的回购

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png) [拉德林斯基](https://github.com/radlinskii) / [围棋场](https://github.com/radlinskii/go-playground)

### 创建存储库是为了和 Golang 一起玩。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 去游乐场

[![Build Status](../Images/f9a08c7ee69f1a725e57e6b66cdedb19.png)](https://travis-ci.com/radlinskii/go-playground)[![GoDoc](../Images/bf166df6f799087994fd5dc11c9d9d2c.png)](https://godoc.org/github.com/radlinskii/go-playground)[![Go Report Card](../Images/c457fdae6c11b8f26916052a9d46abc6.png)](https://goreportcard.com/report/github.com/radlinskii/go-playground)[![gopherbadger-tag-do-not-edit](../Images/a225045c09042918c8acc6ddf2f74315.png)T11】](https://github.com/jpoles1/gopherbadger)

创建存储库是为了和 Golang 一起玩。

</article>

[View on GitHub](https://github.com/radlinskii/go-playground)