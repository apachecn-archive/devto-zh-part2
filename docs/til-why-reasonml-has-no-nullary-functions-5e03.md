# TIL:为什么 ReasonML 没有空函数

> 原文：<https://dev.to/hoichi/til-why-reasonml-has-no-nullary-functions-5e03>

> 为什么 ReasonML 没有空函数？这是由于 ReasonML 总是执行部分应用(后面会详细解释):如果你没有提供一个函数的所有参数，你会从剩下的参数中得到一个新函数的结果。因此，如果您实际上可以不提供任何参数，那么 func()将与 func 相同，并且实际上都不会调用 func。
> 
> –[探索理性和函数式编程【Axel Rauschmayer 博士](http://reasonmlhub.com/exploring-reasonml/ch_functions.html#there-are-no-functions-without-parameters)

* * *

先贴在 [hoichi.io](https://hoichi.io) ，因为不能太偏执。