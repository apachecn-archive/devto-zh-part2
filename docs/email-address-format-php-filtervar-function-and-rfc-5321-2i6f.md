# 电子邮件地址格式、PHP filter_var 函数和 RFC 5321

> 原文：<https://dev.to/hakan/email-address-format-php-filtervar-function-and-rfc-5321-2i6f>

电子邮件地址标准是由 Rfc 5321 设定的。但实际上，事情会有所改变。Rfc5321 告诉我们，电子邮件地址可能是这样的:

```
local-part@domain 
```

域部分可以是任何域或子域，甚至可以是非 tld 的本地域。但是对于本地部分有以下限制。

```
- A-Z, a-z latin letters
- 0-9 digits
- Not to be the first and last character, dot (.) character
- Special characters !#$%&'*+-/=?^_`{|}~ 
```

我们熟悉特殊字符之间的-和 _ 字符，但其他特殊字符一点也不常见。虽然谷歌的电子邮件服务不允许来自流行电子邮件服务的特殊字符，但微软的电子邮件服务只允许-和 _ 特殊字符。对于不同的服务，以下地址可能是可行的。

```
john`doe{software-developer}@domain.sub-domain.com 
```

当 PHP filter_var 函数用于控制邮件地址格式时

```
filter_var('john`doe{software-developer}@domain.sub domain.com',FILTER_VALIDATE_EMAIL) 
```

对于 Rfc 5321 中描述的格式，此函数不会返回 false。但是如果域部分是 gmail，我们会接受一个不应该是 gmail 的邮件地址，因为 Gmail 的实现是不同的。当你试图用一个非标准的库或者一个不同的方法来验证时，事情会变得难以忍受。在一种可能的情况下，根据 Rfc 5321 实现的另一个电子邮件服务的流行电子邮件服务的验证将失败。在一个多语言系统中，如果你有不同语言的电子邮件服务，结果将是一场灾难！