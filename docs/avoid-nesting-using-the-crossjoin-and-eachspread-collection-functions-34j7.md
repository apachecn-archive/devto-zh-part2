# ★使用 crossJoin 和 eachSpread 集合函数避免嵌套

> 原文：<https://dev.to/freekmurze/avoid-nesting-using-the-crossjoin-and-eachspread-collection-functions-34j7>

在处理一个包时，我发现自己在尝试合并一些数据。这是我的代码的样子。假设`$locales`和`$fields`是已经填充的数组。

```
collect($locales)->each(function (string $locale) {
    collect($fields)->each(function (Field $field) use ($locale) 
       $this->doSomeWork($locale, $field); 
    });
}); 
```

当然，这已经很清楚了，但是还有两个很酷的收集方法可以让我们做同样的事情。 [`crossJoin`](https://laravel.com/docs/5.7/collections#method-crossjoin) 用给定输入的所有可能组合创建一个新集合。 [`eachSpread`](https://laravel.com/docs/5.7/collections#method-eachspread) 是一个将每个嵌套项作为参数传递给回调的方法。

使用这些函数，上面的代码可以重写为:

```
collect($this->locales)
    ->crossJoin($this->originalFields)
    ->eachSpread(function (string $locale, Field $field) {
        $this->doSomeWork($locale, $field);
    }); 
```

尽管代码的第一个版本非常清晰，但我更喜欢这个版本。这里不涉及嵌套，我们避免了那个该死的`use`导入变量的需要。

感谢 [Seb](https://twitter.com/sebdedeyne) 的这次重构！