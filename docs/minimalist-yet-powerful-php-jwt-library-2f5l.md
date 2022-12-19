# 简约而强大的 PHP JWT 库

> 原文：<https://dev.to/adhocore/minimalist-yet-powerful-php-jwt-library-2f5l>

JWT 很酷。但不幸的是，几乎所有的 JWT 实现都过于简洁、复杂、臃肿，并提供了大量的公共 API、复杂的配置方法等，这对于试图在任何基于 PHP 的 web 应用程序中集成基于 JWT 的 auth 的初学者来说都是令人生畏和困惑的。这是一个功能全面、小巧、无依赖性、框架不可知的库，我是本着简单的原则编写的。它已经开发了几个月了。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[ad hoc ore](https://github.com/adhocore)/[PHP-jwt](https://github.com/adhocore/php-jwt)

### 超轻量级、无依赖性和独立的 JSON web token (JWT)库，适用于 PHP5.6 到 PHP8.1。这个库使 JWT 成为一块奶酪。

<article class="markdown-body entry-content container-lg" itemprop="text">

## adhocore/jwt

如果你是第一次来 JWT，或者想重温一下对它的熟悉，请查看 [jwt.io](https://jwt.io/)

[![Latest Version](img/2c1a5ea63187fbd812664b9e93c431d4.png)](https://github.com/adhocore/php-jwt/releases)[![Build](img/a0bf595bdb89402ceb54fe846e700bf9.png)](https://github.com/adhocore/php-jwt/actions/workflows/build.yml)[![Scrutinizer CI](img/4c412113d864e8a8edbc686d0e8b1825.png)](https://scrutinizer-ci.com/g/adhocore/php-jwt/?branch=master)[![Codecov branch](img/c1ef4e068d9401fecb991d1db9d598ff.png)](https://codecov.io/gh/adhocore/php-jwt)[![StyleCI](img/e1d0ce5a7751db242fa70680495fdc13.png)](https://styleci.io/repos/88168137)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/adhocore/php-jwtLICENSE)[![Donate 15](img/b2c2f94625d612f1d6f170d10bb8de6d.png)](https://www.paypal.me/ji10/15usd)[![Donate 25](img/55169c512eb5e340a3267ace0ef15ff2.png)](https://www.paypal.me/ji10/25usd)[![Donate 50](img/f0d5a67bfcb273a3a2b8548db2a580d6.png)](https://www.paypal.me/ji10/50usd)[![Tweet](img/3aedcf72e57e3736cd6ea05d11dfd7db.png)](https://twitter.com/intent/tweet?text=Lightweight+JSON+Web+Token+JWT+library+for+PHP7&url=https://github.com/adhocore/php-jwt&hashtags=php,jwt,auth)

*   PHP7 的轻量级 JSON Web 令牌(JWT)库。
*   如果你还在用 PHP5.6，就用版本 [0.1.2](https://github.com/adhocore/php-jwt/releases/tag/0.1.2)

## 装置

```
# PHP7.0+
composer require adhocore/jwt
# PHP5.6
composer require adhocore/jwt:0.1.2

# For PHP5.4-5.5, use version 0.1.2 with a polyfill for https://php.net/hash_equals
```

Enter fullscreen mode Exit fullscreen mode

## 特征

*   支持六种算法:

```
'HS256', 'HS384', 'HS512', 'RS256', 'RS384', 'RS512' 
```

*   `kid`支持。
*   余量支持 0-120 秒。
*   测试的时间戳欺骗。
*   对`RS*`算法的密码短语支持。

## 使用

```
use Ahc\Jwt\JWT;

// Instantiate with key, algo, maxAge and leeway.
$jwt = new JWT('secret', 'HS256', 3600, 10);
```

Enter fullscreen mode Exit fullscreen mode

> 只有密钥是必需的。其余部分将使用默认值:

```
$jwt = new JWT('secret');
// algo = HS256, maxAge = 3600, leeway = 0
```

Enter fullscreen mode Exit fullscreen mode

> 对于`RS*`算法，密钥应该是一个…

</article>

[View on GitHub](https://github.com/adhocore/php-jwt)

**安装**
`composer install adhocore/jwt`

## 用法

```
use Ahc\Jwt\JWT;

// Instantiate with key, algo, maxAge and leeway.
$jwt = new JWT('secret', 'HS256', 3600, 10);

// Only the key is required. Defaults will be used for the rest:
// algo = HS256, maxAge = 3600, leeway = 0
$jwt = new JWT('secret');

// For RS* algo, the key should be either a resource like below:
$key = openssl_pkey_new(['digest_alg' => 'sha256', 'private_key_bits' => 1024, 'private_key_type' => OPENSSL_KEYTYPE_RSA]);
// OR, a string with full path to the RSA private key like below:
$key = '/path/to/rsa.key';
// Then, instantiate JWT with this key and RS* as algo:
$jwt = new JWT($key, 'RS384');

// Generate JWT token from payload array.
$token = $jwt->encode([
    'uid'    => 1,
    'aud'    => 'http://site.com',
    'scopes' => ['user'],
    'iss'    => 'http://api.mysite.com',
]);

// Retrieve the payload array.
$payload = $jwt->decode($token);

// Oneliner.
$token   = (new JWT('topSecret', 'HS512', 1800))->encode(['uid' => 1, 'scopes' => ['user']]));
$payload = (new JWT('topSecret', 'HS512', 1800))->decode($token);

// Can pass extra headers into encode() with second parameter.
$token = $jwt->encode($payload, ['hdr' => 'hdr_value']);

// Spoof time() for testing token expiry.
$jwt->setTestTimestamp(time() + 10000);
// Throws Exception.
$jwt->parse($token);

// Call again without parameter to stop spoofing time().
$jwt->setTestTimestamp(); 
```

Enter fullscreen mode Exit fullscreen mode

为了让您安心，请允许我提一下，这个库已经被[正式上市](https://github.com/jsonwebtoken/jsonwebtoken.github.io/pull/337)所采用。