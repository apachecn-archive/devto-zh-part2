# PHP 面试问题

> 原文：<https://dev.to/sharadtricks/php-interview-questions-16kp>

下面是几个最新的 Php 编程面试问题

1.  PHP 是什么？

PHP:超文本预处理器是一种开源的服务器端脚本语言，广泛用于创建动态 web 应用程序。它是由 PHP 之父拉斯马斯·勒德尔夫于 1994 年开发的。

PHP 是一种松散类型的语言，我们不必告诉 PHP 变量是哪种数据类型。PHP 根据变量的值自动将其转换为正确的数据类型。

1.  T _ PAAMAYIM _ NEKUDOTAYIM 是什么？

T _ PAAMAYIM _ NEKUDOTAYIM 是作用域解析运算符，用作::(双冒号)。基本上，它习惯于调用类的静态方法/变量。

用法示例:-

$ Cache::getConfig($ key)；

1.  PHP 中==和===运算符有什么区别？

在 PHP 中==是等于运算符，如果$a 等于$b，则在类型转换后返回 TRUE，并且===是相同的运算符，如果$a 等于$b，则返回 TRUE，并且它们属于相同的数据类型。

示例用法:

$ a =真；
$ b = 1；
// Below 条件返回 true 并打印 a 和 b 相等
if($a==$b){
回显“a 和 b 相等”；
}else{
回显“a 和 b 不相等”；
}
//以下条件返回 false，打印 a 和 b 不相等，因为$a 和$b 属于不同的数据类型。
if($a===$b){
回显“a 和 b 相等”；
}else{
呼应“a 和 b 不相等”；
}
？>

1.  PHP 中的 session 是什么？如何从会话中删除数据？

因为 HTTP 是状态协议。在服务器上维护状态和跨多个页面共享数据 PHP 会话是 used.PHP 会话是根据唯一会话 ID 为单个用户/客户端存储数据的简单方法。会话 id 通常通过会话 cookies 发送到浏览器，该 ID 用于检索现有的会话数据，如果服务器上没有会话 ID，PHP 将创建一个新的会话，并生成一个新的会话 ID。

用法示例:-

//启动会话
session _ start()；
//创建会话
$ _ SESSION[' user _ info ']=[' user _ id ' =>1，' first _ name ' =【T5]' PHP '，' last_name'= > 'scots '，' status ' =【T7]' active ']；

//检查会话
if(is set($ _ SESSION[' user _ info ']){
回显“已登录”；
}

//un setting remove a value from SESSION
unset($ _ SESSION[' user _ info '][' first _ name '])；

//销毁完整会话
session _ destroy()；

？>

1.  如何在 PHP 会话中注册变量？

在 PHP 5.3 或更低版本中，我们可以注册一个变量 session_register()函数。现在不推荐使用它，我们可以直接在$_SESSION Global 中设置一个值。

示例用法:

//开始会话
session _ start()；
//不推荐使用 session _ register()
$ username = " PHP Scots "；
session_register("用户名")；
//优先使用$ _ SESSION
$ _ SESSION[" username "]= " PHP Scots "；
？>

也请阅读 Laravel 面试问题

1.  会话存储在 PHP 的什么地方？

PHP 会话通常存储在服务器的临时目录下的文本文件中。从外部 word 无法访问该文件。当我们创建一个会话时，PHP 通过在客户端浏览器上创建 cookie 来创建一个唯一的会话 id。每次发出请求并识别会话时，客户端浏览器都会将该会话 id 发送到服务器。
默认的会话名称是“PHPSESSID”。

1.  PHP 中的默认会话时间和路径是什么？怎么改？

PHP 中的默认会话时间是 1440 秒(24 分钟)，默认会话存储路径是服务器上的临时文件夹/tmp。

您可以使用以下代码更改默认会话时间

//服务器应该保存会话数据至少 1 小时
ini _ set(' session . GC _ max lifetime '，3600)；

//每个客户端应该记住它们的会话 id 正好 1 小时
session _ set _ cookie _ params(3600)；
？>

1.  什么是 PHP 神奇的方法/函数。列出它们。

在 PHP 中，所有以 __ 名开头的函数都是神奇的函数/方法。神奇的方法总是存在于 PHP 类中。神奇功能的定义是由程序员自己定义的。

以下是 PHP 中可用的神奇函数列表

__construct()、__destruct()、__call()、__callStatic()、__get()、__set()、__isset()、__unset()、__sleep()、__wakeup()、__toString()、__invoke()、__set_state()、__clone()和 __debugInfo()。

1.  include、require、include_once 和 require_once()之间有什么区别？

Include :-Include 用于在一个 PHP 脚本中多次包含文件。您可以根据需要多次包含一个文件。

语法:-include(" file _ name . PHP ")；

Include Once:-Include once 在 php 脚本中只包含一个文件一次。第二次尝试包含被忽略。

语法:-include _ once(" file _ name . PHP ")；

Require:-Require 也用于在一个 PHP 脚本中多次包含文件。如果在指定的位置或路径找不到文件，Require 将生成致命错误并暂停脚本执行。您可以在一个脚本中多次请求一个文件。

语法:-require(" file _ name . PHP ")；

要求一次:-要求一次在 php 脚本中只包含一个文件一次。第二次尝试包含被忽略。如果在指定的位置或路径找不到文件，Require Once 也会生成致命错误并暂停脚本执行。

语法:-require _ once(" file _ name . PHP ")；

阅读更多问题来自[https://www . online interview Questions . com/core-PHP-interview-Questions/](https://www.onlineinterviewquestions.com/core-php-interview-questions/)