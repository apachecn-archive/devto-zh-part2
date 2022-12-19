# PHP 安全性:密码

> 原文：<https://dev.to/devmazee2057282/php-security-passwords-1moi>

# 我的密码规则

*   最小长度为 16 个字符。
*   最大长度 256，512，1，024 或 2，048 个字符。
*   确保密码没有被篡改。
*   **允许粘贴密码！**

微软的**最大**密码长度为 *16 个字符。*

你觉得这样好吗？嗯，当然不应该。应用程序通常不会有这么短的最大密码长度(如果我错了，请纠正我)。但是像微软那样限制密码长度真的不好。

是否应该强制执行特殊字符、大写字母、小写字母等密码规则是有争议的。我不认为那是更好的做法。我强烈反对这样的密码规则。

所有的应用程序应该做的是确保我的秘密足够长，并慢慢散列它，这样我的秘密就不会在任何人的有生之年被发现。

许多网站的最小密码长度为 6 或 8 个字符。这可不好。真的不是。即使他们用的是成本很高的 Argon2。12 个字符没问题...但是我喜欢将最小值设置为 16 个字符左右。

为什么允许这么大的最大字符数？太可笑了！

你说得对。它*大概*是。但我喜欢确保密码管理员能够输入超长密码。

# 存储密码

我将向您展示在 PHP >= 5.6 中安全地散列密码是多么容易。是自动加盐的。

```
$hashed_password = password_hash('string', PASSWORD_DEFAULT); 
```

Enter fullscreen mode Exit fullscreen mode

PHP 7.2 及更低版本的默认密码哈希算法是 bcrypt。但是请注意，虽然 bcrypt 很好，但是它将密码截断为 72 个字符，并且容易出现空字节。

我存储密码的首选方式*可能*与你不同。

## 我的手法

*   HMAC 的密码。
*   哈希 HMAC。
*   加密哈希。

你到底为什么要输入密码？！

那是因为当有人注册你的网站时。如果他们输入一个 4 兆字节的密码，并发送*多个*请求会怎么样？这可以有效地 DDoS 你。但是，您可以通过使用快速算法(如 SHA256)进行预哈希来防止这种情况。或者，您可以验证密码，确保它不超过最大字符数。如果它通过了验证，那么你可以散列密码，等等。

但这不是它能解决的唯一问题。如果你在储存大头针呢？只有 4 位数的数字，可以在合理的时间内暴力破解。想想看，你只有 4 个字符。你可以用 HMAC 把 4 个字符变成一个很长的字符串。这就对了，这比只存储 4 个字符要好。

这对你有什么影响？

比如说 Argon2 就需要很大的计算能力。记住，密码散列算法*意味着*很慢。因此，如果散列一个 8 个字符的密码需要 1 秒，那么散列一个 1 兆字节的密码可能需要 10 秒。这就是*为什么*我们将密码缩短或延长到设定的长度，这样我们就不会有这个问题。

但是混淆不同的算法是很危险的！

有可能，但我非常怀疑散列一个 SHA3-512 十六进制输出在 Argon2 散列函数中会有什么不好，因为它实际上只是数字和字母。密码通常不都是这样的吗？

好吧，但是你为什么要加密哈希呢？

如果使用快速加密算法，这不是性能问题。

**静止！为什么？它没有增加任何好处，并且使您的应用程序更难维护。**

这实际上是一个很好的论点。然而，假设您的数据库服务器与您的应用服务器是分开的。**如果**一个攻击者*只有*能够访问数据库，他们首先必须找到加密密钥来解密散列，或者最终破解它，结果只能用一个非常慢的散列密码来阻止他们。但在现实世界中，我怀疑有人会试图暴力破解 AES 加密，他们可能会开始寻找密钥。但是这样做*可以*为你赢得时间，可能有足够的时间来轮换你的密钥并告诉用户他们的数据已经被泄露。

### 代码

我还可以向您展示一个 PHP 的快速基本实现。

首先，我们当然需要一个密码。

```
$password = 'my super secret password'; 
```

Enter fullscreen mode Exit fullscreen mode

现在完成了，我们可以设置 HMAC 的算法和密钥。

```
$hmac_algorithm = 'sha3-512';
$hmac_key       = random_bytes(32); 
```

Enter fullscreen mode Exit fullscreen mode

第一步:制作 HMAC。

```
$hmac = hash_hmac($hmac_algorithm, $password, $hmac_key); 
```

Enter fullscreen mode Exit fullscreen mode

哒哒！现在已经完成了，让我们继续设置密码散列。

```
$password_hash_cost      = 4;
$password_hash_algorithm = PASSWORD_ARGON2I;
$password_hash_options   = [
    'memory_cost' => $password_hash_cost * PASSWORD_ARGON2_DEFAULT_MEMORY_COST,
    'time_cost'   => $password_hash_cost * PASSWORD_ARGON2_DEFAULT_TIME_COST,
    'threads'     => $password_hash_cost * PASSWORD_ARGON2_DEFAULT_THREADS
]; 
```

Enter fullscreen mode Exit fullscreen mode

第二步:散列。

```
$hash = password_hash(
    $hmac,
    $password_hash_algorithm,
    $password_hash_options
); 
```

Enter fullscreen mode Exit fullscreen mode

没错。都搞定了。不过，我们需要设置下一步。

```
$encryption_key   = random_bytes(SODIUM_CRYPTO_SECRETBOX_KEYBYTES);
$encryption_nonce = random_bytes(SODIUM_CRYPTO_SECRETBOX_NONCEBYTES); 
```

Enter fullscreen mode Exit fullscreen mode

第三步:制作密文。

```
$ciphertext = sodium_crypto_secretbox(
    $hash,
    $encryption_nonce,
    $encryption_key
); 
```

Enter fullscreen mode Exit fullscreen mode

我运行了上面的代码，结果如下:

```
HMAC output: 876dd083d78abd57c267a4cb3b64788c468f7ff9a88ab91800e5ae3cc3e25f646510fc2e2a9ccd9395ba01b814dbe76efa2acb985a7733330f4abc6b5157474c

Hash output: $argon2i$v=19$m=4096,t=8,p=8$ZEJXdlN2QU8vb3RIU3RxeA$0hU3ZokcZpPJwfmdmzwXD5KFfdmh/MyZRAFx4tLIJkc

Ciphertext output: 319dfbac430ad505b7daccfb8c827f36389b1dc747d3b3fc6cd1334e060b156800dec5268c79fe46367ad1be3ddac70540ddc19a6fb70348018a5ed27bcd2fb822a83e289833ec6d3294881eed6e45b94fa8c5a6f502ddb0851956587d6a2817bc45f82251b7e633de9cc3c2779e9b 
```

Enter fullscreen mode Exit fullscreen mode

注意，我把密文转换成了十六进制。默认情况下，HMAC 输出转换为十六进制。我当时用的是 PHP 7.2.9，加密用的是 lib 钠。

我绝不是密码学或其实现方面的专家。这是我对密码的看法，你可能会有所不同。

今天就到这里，我希望你喜欢这篇文章，也许你对密码有了更多的了解。

# **编辑请阅读:**

不要用我的技术。看评论。只需使用密码哈希算法，如 Argon2 或 Bcrypt 来存储您的密码。

就像我之前说的。如果您将**应用程序和数据库放在不同的服务器/硬件上，**假设攻击者只能访问您的数据库，那么加密密码散列才有效。