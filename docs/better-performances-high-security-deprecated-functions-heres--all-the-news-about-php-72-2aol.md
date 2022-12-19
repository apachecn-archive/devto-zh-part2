# 更好的性能，高安全性，弃用的函数:这里是关于 PHP 7.2 的所有消息

> 原文：<https://dev.to/minompi/better-performances-high-security-deprecated-functions-heres--all-the-news-about-php-72-2aol>

PHP 7.2 有许多关于安全、实现和不推荐使用的功能的特性。
此外，它引入了性能提升(已经在版本 7 中完成了一项伟大的工作):PHP 7.2 比版本 7.0 快 20%，比版本 7.1 快 10%。

这里是这个新版本提供的所有**亮点**。

## 输入提示参数

从 PHP 5 开始，可以指定传递给函数的参数类型。

示例:

```
class SimpleClass {
    public $foo = 'bar';
}

$simpleClass = new SimpleClass;

function test(SimpleClass $simpleClass){
    return $simpleClass->foo;
}

echo test($simpleClass); 
```

Enter fullscreen mode Exit fullscreen mode

从 PHP 7.1 开始，如果向函数传递一个非简单类类型的值，就会产生一个 500 错误。

在 PHP 7.2 中，**类型提示参数**可以被声明为 object，这意味着它是一个通用对象。

示例:

```
class SimpleClass {
    public $foo = 'bar';
}

class FirstClass extends SimpleClass {
    public $foo = 'baz';
}
class SecondClass extends SimpleClass{
    public $foo = 'foobar';
}

$firstClass = new FirstClass;
$secondClass = new SecondClass;

function test(object $arg)
{
    return $arg->var;
}

echo test($firstClass);

echo test($secondClass); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，代码运行时先声明参数对象类型，然后声明一个泛型对象，这样就不会产生 500 错误。
此外，这个特性允许在子类中实现一个类型“方差”。

对象从 7.2 版开始成为一个键值，所以一定不要将它用于类名、接口或特征。

## 键入提示返回值

从 7.2 版本开始，可以将**类型提示返回值**声明为对象。

示例:

```
function test($arg) :object
{
    return new Foo($arg);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 抽象方法和可重写接口

PHP 7.2 允许**覆盖抽象方法和接口**，即省略父类扩展的抽象方法中参数的声明类型。

示例:

```
abstract class foo
{
    abstract function baz(string $yourVar);
}

abstract class bar extends foo
{
    abstract function baz($yourVar) : int;
} 
```

Enter fullscreen mode Exit fullscreen mode

同样的功能也适用于接口，例如:

```
interface foo
{
    public function test(string $yourVar);
}

class bar implements foo
{
    public function test($yourVar)
    {
        //your code
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个新特性允许更新父类而不更新它的所有子类。

大概这是一个边际改进(绝不会用的这么频繁)。

## 逗号放入语法末尾的 use 语句列表中

逗号在最后一个数组元素的末尾，这在 PHP 语法中是有效的，有时我们鼓励通过省略逗号来轻松地添加新元素并避免分析错误。

从 PHP 7.2 版本开始，甚至在类内部的“use”中也可以使用逗号。

示例:

```
use Foo\Bar\{
    Foo,
    Bar,
    Baz,
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 密码散列中的 Argon2

Argon2 是一种强大的哈希算法，被选为 2015 年密码哈希竞赛的获胜者，PHP 7.2 引入了 Bcrypt 算法的安全替代方案。

新的 PHP 版本引入了 PASSWORD_ARGON2I 常量，现在可以在 password_ *函数中使用该常量，语法如下:

```
password_hash('secretpassword', PASSWORD_ARGON2I); 
```

Enter fullscreen mode Exit fullscreen mode

尽管 Bcrypt 只需要一个因素，但 Argon2 考虑了三个不同的因素:

*   定义散列期间使用的 Kib 数的存储器成本(标准值是 1 << 10 或 1024 KiB 或 1 MiB)；
*   定义哈希算法交互次数的时间成本(标准值为 2)；
*   parallelism 因子，设置哈希过程中使用的并行线程数(标准值为 2)；

三个新的成本要素定义了标准成本的因素:

```
1\. PASSWORD_ARGON2_DEFAULT_MEMORY_COST

2\. PASSWORD_ARGON2_DEFAULT_TIME_COST

3\. PASSWORD_ARGON2_DEFAULT_THREADS 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
$options = ['memory_cost' => 1<<11, 'time_cost' => 4, 'threads' => 2];
password_hash('secretpassword', PASSWORD_ARGON2I, $options); 
```

Enter fullscreen mode Exit fullscreen mode

## lib 钠作为 PHP 核心的一部分

从 7.2 版本开始，PHP 在其核心中包含了**钠库**。libna 是一个跨平台和跨语言的库，用于加密、解密、签名、密码散列等等。
该图书馆以前由 PECL 使用。
PHP 7.2 是第一种在其库中加入现代加密的编程语言。

## 统计标量值

您知道计算标量值是可能的吗？实际上这并不重要，因为函数 count()对于标量只返回 1。

示例:

```
var_dump(count(null)); // int(0)
var_dump(count(0)); // int(1)
var_dump(count(4)); // int(1)
var_dump(count('4')); // int(1) 
```

Enter fullscreen mode Exit fullscreen mode

从 PHP 7.2 开始，会显示一条消息:

```
Warning: count(): The parameter must be an array or an object that implements Countable into / in / 4aIl2 on line 3. 
```

Enter fullscreen mode Exit fullscreen mode

## 新增功能

PHP 7.2 引入了一些非常有用的新特性:ftp_append()、hash_hmac_algos()、imagesetclip()、imagegetclip()、imageopenpolygon()、imageresolution()、imagecreatefrombmp()、imagebmp()。

## 弃用的功能

**assert()** 函数验证数据断言，如果结果为假，则采取适当的措施。
使用带有字符串参数的 assert()现在已被弃用，因为它打开了一个 RCE 漏洞。

PHP 5.1 中的 **__autoload** 函数已经被 spl_autoload_register 代替。
现在当它被揭露时，在编译过程中会产生一个反对信息。

当抛出非致命错误时，在局部范围内创建 **$php_errormsg** 变量。因为 PHP 7.2 应该用 error_get_last 和 error_clear_last 来代替。

**create_function()** 允许使用生成的函数名、参数列表和作为参数传递的代码体创建函数。由于安全问题和不充分的性能，它已被标记为不推荐使用(否则建议使用 enclosure)。

**each()** 用于迭代类似 foreach()的数组，速度快 10 倍。

**$errcontext** 是一个数组，包含错误生成时刻的本地现有变量。

**gmp_random()** 被认为依赖于平台，将被弃用。否则建议使用 gmp_random_bits()和 gmp_random_rage()。

对于 **mbstring.func_overload** ，插入到不同于零的默认值中的 ini 设置已被标记为不推荐使用。

**parse_str()** 如果传递了第二个参数或者没有使用本地符号表，则分析数组中的查询字符串。因为出于安全原因，不鼓励函数的动态变量设置，所以在没有第二个参数的情况下使用 parse_str()将会生成一个不推荐使用的建议。

**(未设置)cast** 是一个总是返回 null 的表达式，被认为是无用的。

欲了解更多信息，请联系我或关注我:

[推特](https://twitter.com/minompi)
T3】GitHub