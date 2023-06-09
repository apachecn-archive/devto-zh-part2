# 总是添加大括号

> 原文：<https://dev.to/timabell/always-add-braces-1c68>

我一直遇到的代码风格的小问题。

是否写

```
if (thing) Action(); 
```

或者

```
if (thing)
{
    Action();
} 
```

我们应该总是使用后者，以避免在手动或通过合并工具修改代码时引入错误。

出于这背后的原因，请阅读以下内容:

*   [https://www . joelonsoftware . com/2005/05/11/making-error-code-look-error/](https://www.joelonsoftware.com/2005/05/11/making-wrong-code-look-wrong/)
*   [https://dzone.com/articles/omitting-braces-not-just-a-mat](https://dzone.com/articles/omitting-braces-not-just-a-mat)
*   [https://naked security . sophos . com/2014/02/24/anatomy-of-a-goto-fail-apple-SSL-bug-explained-plus-an-official-patch/](https://nakedsecurity.sophos.com/2014/02/24/anatomy-of-a-goto-fail-apples-ssl-bug-explained-plus-an-unofficial-patch/)