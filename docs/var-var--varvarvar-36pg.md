# var = var()中的 var.var()

> 原文：<https://dev.to/bufferings/var-var--varvarvar-36pg>

`var`是 Java10 中引入的。很有意思！

```
jshell> Var var(){return new Var();}
|  created method var(), however, it cannot be referenced until class Var is declared

jshell> class Var{Var1 var = new Var1();}
|  created class Var, however, it cannot be referenced until class Var1 is declared

jshell> class Var1{Var var(){return new Var();}}
|  created class Var1

jshell> var var = var().var.var();
var ==> Var@58651fd0 
```

Enter fullscreen mode Exit fullscreen mode