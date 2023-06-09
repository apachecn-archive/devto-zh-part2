# Laravel 多租户:路由模型绑定

> 原文：<https://dev.to/lloople/laravel-multitenancy-route-model-binding-5ea9>

为什么要写这样的代码

```
public function edit($articleId)
{
    $article = Article::findOrFail($articleId);

    return view('articles.edit', compact('article')); 
} 
```

当你可以简单地做

```
public function edit(Article $article)
{
    return view('articles.edit', compact('article'));
} 
```

自从我发现《T2》这部电影以来，我就是它的超级粉丝。你只需要在 functiondeclaration 中输入提示模型，框架就会为你处理`findOrFail`。就是这样。

但它有一个问题，有时你需要检查其他领域。

使用我们之前的例子:

```
public function edit($articleId)
{
    $article = Article::where('tenant_id', auth()->user()->tenant_id)->findOrFail($articleId);

    return view('articles.edit', compact('article'));
} 
```

会比
看起来更干净

```
public function edit(Article $article)
{
    abort_unless($article->tenant_id === auth()->user()->tenant_id);

    return view('articles.edit', compact('article'));
} 
```

这是一个常见的场景。你有一个编辑文章的应用程序，你必须阻止你的用户编辑其他用户的文章。基本安全规则。但是你不喜欢那种需要读两遍才能一看就明白它是干什么的代码。

当然，你可以使用[全局作用域](https://laravel.com/docs/5.7/eloquent#global-scopes)，但是它们有一个问题:全局作用域将被应用于该模型上的所有查询，不管它们出现在应用程序的什么地方。因此，当您需要查询所有文章进行统计或维护时，每次都需要将`withoutGlobalScope`添加到查询中。

我和我的团队找到的解决方案是覆盖路由模型绑定的默认行为。当然，Laravel 为你提供了一个简单的解决方案，你只需要在你的模型中覆盖一个方法就可以让它运行起来。

这是默认行为:

```
/**
 * Retrieve the model for a bound value.
 *
 * @param mixed $value
 * @return \Illuminate\Database\Eloquent\Model|null
 */
public function resolveRouteBinding($value)
{
    return $this->where($this->getRouteKeyName(), $value)->first();
} 
```

这就是我们需要在`Article`模型中声明的内容:

```
/**
 * Retrieve the model for a bound value.
 *
 * @param mixed $value
 * @return \Illuminate\Database\Eloquent\Model|null
 */
public function resolveRouteBinding($value)
{
    return $this->where($this->getRouteKeyName(), $value)
        ->where('tenant_id', auth()->user()->tenant_id)
        ->first();
} 
```

我知道你要说什么

> 但是 David，如果我们在控制器中检查这一点，就很容易理解代码做了什么

是的，当然，我不会对每一种过滤都这样做，比如删除一个有截止日期的`PromoCode`。但是当你有一个多租户应用程序时，这种授权是值得赞赏的。

我的哲学是:

> 控制器应该只接收它可以工作而不检查条件的实体。

我也将这个规则应用于使用[表单请求](https://laravel.com/docs/5.7/validation#form-request-validation)的`Request`验证。当然，你会发现由于某种复杂和神秘的检查，这个规则有时无法应用，但这就是编程的意义所在🙂。