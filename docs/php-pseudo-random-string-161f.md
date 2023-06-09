# PHP 伪随机字符串

> 原文：<https://dev.to/czarpino/php-pseudo-random-string-161f>

在用 PHP 编写随机字符串生成器时，您必须首先考虑是否需要一个用于一次性字符串(如占位符或示例用户名)或敏感数据(如默认密码、应用程序令牌或 salt)的生成器。如果您正在为后者生成，那么这篇文章就是为您准备的。

### 任意长度的字符串

在 PHP 中生成随机字符串包括两个主要步骤:

1.  首先是生成一组加密安全的随机字节
2.  第二步是将字节编码成可打印的字符串

对于生成随机字节，取决于 PHP 版本，有两种主要的方法。PHP7 之前的方法使用 [`openssl_random_pseudo_bytes`](http://php.net/manual/en/function.openssl-random-pseudo-bytes.php) ，而 PHP7 的方法更喜欢 [`random_bytes`](http://php.net/manual/en/function.random-bytes.php) 。后者的一个优点是它是本机的，不需要安装额外的模块。如果需要，`openssl_random_pseudo_bytes`仍然可以在 PHP7 中作为扩展使用。但是因为这两个函数都被认为是密码安全的生成器，所以推荐使用`random_bytes`函数。

对于将字节编码成可打印的字符，也有几种可行的方法。其中最受欢迎的是 [`base64_encode`](http://php.net/manual/en/function.base64-encode.php) 和 [`bin2hex`](http://php.net/manual/en/function.bin2hex.php) 。一般来说，这两种方法都不会错，尽管`bin2hex`的优点是长度更容易预测，而`base64_encode`产生的字符串更短，使用的字符更多样。

具体来说，下面是如何生成加密安全的随机字符串:

```
// Pre PHP7
$numOfBytes = 10;
$randomBytes = openssl_random_pseudo_bytes($numOfBytes);
$randomString = base64_encode($randomBytes);

/// PHP7+
$numOfBytes = 10;
$randomBytes = random_bytes($numOfBytes);
$randomString = base64_encode($randomBytes); 
```

如果您需要某种可预测性，比如生成特定长度的字符串，那么使用`bin2hex`对字节进行编码会更合适，尽管仅限于偶数长度的字符串。

```
// Need to generate string with length 20!

// First generate 10 random bytes
$randomBytes = random_bytes(10);

// Now encode to get a 20 character string
$randomString = bin2hex($randomBytes); 
```

#### 精确长度字符串

如果您想要完全控制生成的随机字符串的长度，使用 PHP7 的`random_int`函数会更合适。如果你的系统在 PHP5 上，你可以使用一个名为 [`random_compat`](https://github.com/paragonie/random_compat) 的聚合填充库。

```
// Generate a 20 character string
$randomStr = '';
$allowedCharacters='0123456789abcdef';
for (
    $i = 0, $allowedMaxIdx = mb_strlen($allowedCharacters) - 1;
    $i < 20;
    $i ++
) {
    $randomStr .= $allowedCharacters[random_int(0, $allowedMaxIdx)];
} 
```

这就是如何在 PHP 中生成安全的随机字符串。编码快乐！

*最初发表于[https://plog.czarpino.com/php-pseudorandom-string/](https://plog.czarpino.com/php-pseudorandom-string/)T3】*