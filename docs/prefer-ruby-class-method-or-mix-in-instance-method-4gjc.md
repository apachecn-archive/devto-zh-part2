# 喜欢 Ruby 类方法还是 Mix-In 实例方法？

> 原文：<https://dev.to/burdettelamar/prefer-ruby-class-method-or-mix-in-instance-method-4gjc>

假设某种功能在 Ruby 中既可以作为类方法使用，也可以作为实例方法使用(通过模块)。

类方法:

```
require 'foo'  
Foo.foo() 
```

Enter fullscreen mode Exit fullscreen mode

实例方法(通过模块):

```
require 'bar'    
include Bar    
bar() 
```

Enter fullscreen mode Exit fullscreen mode

假设这两种方法的行为相同，你更喜欢使用哪一种，为什么？

(注意，问题不在于实现哪一个，而在于如果两者都可用，使用哪一个。)