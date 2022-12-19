# 实现 TOTP

> 原文：<https://dev.to/nasa9084/totp-5doe>

[![TOTPを実装する](img/8468dba9a2b242fb1c5997921d4ca285.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hcsFDC6R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/conteimg/2018/03/gopher-5.png)

这是近几年很多服务都采用的双因素认证，大家在用吗？

我其实直到最近都很麻烦，很少用，终于沉重地挺起腰到处设定了。

其中，近年来特别常用的是被称为 totp ( time-based one-time password )的算法。

TOTP 算法是在[RFC6238](https://tools.ietf.org/html/rfc6238) 中定义的算法，用于生成服务器和客户端共享的密钥以及从当前时间开始确认的代码。

看 RFC 和[维基百科](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm)就可以知道，用相当简单的算法，一个一个理解的话就可以比较容易地实现。

以 Go 语言的代码为实例，尝试实现示例代码。

## 的热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式热压式

与 TOTP 算法非常相似的是一种称为 TOTP (基本一次性密码)的算法。

这是根据服务器和客户端共享的私钥和“第几次认证”生成确认用代码的算法。

HOTP 算法是(当然)算法，所以是某个计算步骤，可以将私钥和认证次数作为自变量返回认证码的函数来表示。

对于这个认证次数这个自变量，输入当前时间的就是 TOTP。

因为认证只有“次数”，所以值是正整数。 使用 UnixTime 将时间输入为整数。

实际上，如果直接输入 UnixTime 的话，认证码会每一秒钟发生变化，无法实用，所以以某个秒数为一个周期，输入现在是第几个周期的值。

## 安装 TOTP

前言就这么多，实现 TOTP 算法。

用以下公式表示。

$$TOTP(K，T_0，X) = HOTP(K，T(T_0，X))$ $
T1】$ $ T(T _ 0，X)= \ frac {(current unixtime-T _ 0)} { X } $ $

*   $K$是共享私钥。
*   $T_0$是开始计数的时间，通常使用 Unix epoch，即 0。
*   $X$是一个周期的秒数，规定值为 30 秒。 (实际上，很多服务都是基于 30 秒的)

程序实现试着写如下。

```
func TOTP(k string, t0, x int) int {
    return HOTP(k, T(t0, x))
}

func T(t0, x int) int {
    return (time.Now().Unix - t0)/x
} 
```

很简单啊。 上述内容中，没有定义的只有`HOTP(K, T)`。

## 安装 HOTP

TOTP 的代码中没有实现 HOTP 算法部分，实现这里应该可以实际使用。

HOTP 算法在[RFC4226](https://tools.ietf.org/html/rfc4226) 中进行了定义，所以在阅读的同时进行实施。

阅读 RFC 后，您会发现 HOTP 大致需要以下三个步骤。

1.  根据共享私钥和认证次数求出 HMAC-SHA1 的值
2.  生成 4byte 的字符串
3.  计算热值

不知是什么啊。 让我们再仔细看看。

### 1 .求出 1\. HMAC-SHA1 的值

HMAC 是一种消息认证码，它使用散列函数从私钥和消息中生成认证码。

在这里，正如其名，作为散列函数使用 SHA1。

另外，使用认证次数作为消息。

将 HMAC-SHA1 的值设为$HS$，用公式表示的话，形式如下。

[

HS = HMAC-SHA1(K，C)

*   $K$是私钥
*   $C$是消息(这里是认证次数)

在 Go 语言中，HMAC 和 SHA1 都在标准包中，所以使用它们。

HMAC 为[`crypto/hmac`](https://golang.org/pkg/crypto/hmac)封装，SHA1 为[`crypto/sha1`](https://golang.org/pkg/crypto/sha1)封装。

我会试着实现。

```
func HMACSHA1(k, c []byte) []byte {
    mac := hmac.New(sha1.New, k)
    mac.Write(c)
    return mac.Sum(nil)
} 
```

在 Go 语言中，HMAC 被实现为`hash.Hash`，通过`hmac.New(func() hash.Hash, []byte)`得到散列函数对象后使用。

SHA1 的块长<sup>[[1]](#fn1)</sup> 为 160bit，所以用字符串来说就是得到 20 个字符的字符串。

### 生成 2\. 4byte 的字符串

然后，创建之前计算的$HS$到 4byte 的字符串。

RFC 的第 6 页上写了计算方法，照做计算。

[

]Sbits = DT(HS)

]]

首先，求`offsetbits`。

`offsetbits`是$HS$的第 20 个字符(即最后一个字符)的后 4 位。

因为用 Go 语言处理的是 byte 列，所以假设将第 20 个字符全部取出 8bit，用 0 填充前 4bit (用$00001111_{(2) }$进行屏蔽)，从而取出后 4bit

此外，在 Python 等中也有二进制文字，但在 Go 中没有二进制文字，所以用十六进制表示( $00001111_{(2) }$为$F_{(16 ) }

```
offsetbits := hs[19] & 0xF 
```

接下来，求`offset`。

`offset`将`offsetbits`作为数值取出。

这不是进行特别变换，而是将 byte 列直接作为 int 读取(因为是 4bit，所以是 0〜15 的值)。

```
offset := int(offsetbits) 
```

接着，从$HS$的第`offset`个字符中提取 4 个字符，将其作为`p`。

```
p := hs[offset:offset+4] 
```

拔出`p`的终端 31bit。

这也和`offsetbits`时的计算一样，通过戴上口罩进行同样的处理( $ 0111111111111111111111 _ { (2) }$为$7ffffff _ { }

另外，直接给 byte 列戴口罩有点麻烦，下一步会进行向数值的转换，所以先变成数值后再戴口罩。
由于无法从
`[]byte`直接转换为`int`，所以使用[`encoding/binary`](https://golang.org/pkg/encoding/binary)包装。

到此为止，总结如下。

```
func DT(hs []byte) int {
    offsetbits := hs[19] & 0xF
    offset := int(offsetbits)
    p := hs[offset:offset+4]
    return int(binary.BigEndian.Uint32(p)) & 0x7FFFFFFF
} 
```

### 3 .计算 hotp 的值

取上一步得出的值的数值表示和$10 <sup>{Digit}$的剩余，存储到规定的位数中。</sup>

幸运的是，前一步的输出值为`int`，所以几乎可以直接使用。

```
func ReductionModulo(snum int) int {
    return int(int64(snum) % int64(math.Pow10(g.Digit)))
} 
```

### HOTP 算法总结

结合到目前为止看到的步骤，创建得到 HOTP 的函数吧。

再次审视 RFC，HOTP 被定义如下。

【

HOTP(K，C) =截断(HMAC-SHA-1(K，C))

$Truncate$是指`DT`。

用 Go 语言将其重写为如下。

```
func HOTP(k, c []byte) int {
    return DT(HMACSHA1(k, c))
} 
```

## 在 TOTP 的同时修正 HOTP

顺便说一下，本文的目标是实现 TOTP 算法。

在本文开头创建的 TOTP 函数如下。

```
func TOTP(k string, t0, x int) int {
    return HOTP(k, T(t0, x))
}

func T(t0, x int) int {
    return (time.Now().Unix - t0)/x
} 
```

`T(t0, x)`的返回值为`int`，所以无法传递给`func HOTP(k, c []byte) int`。

这里试着修正一下 HOTP 函数。

```
func HOTP(k []byte, c int) int {
    cb := make([]byte, 8)
    binary.BigEndian.PutUint64(cb, c)
    return DT(HMACSHA1(k, cb))
} 
```

根据希望在`TOTP`函数中给出的形式，在函数内加入了类型转换逻辑。

这样，就可以顺利地实现 TOTP 算法了。

(正文中的源代码未经测试。 **请勿在正式演出中使用**)

* * *

1.  输出的长度