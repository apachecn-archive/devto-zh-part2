# 避免 ASP.NET 核心中的异常

> 原文：<https://dev.to/sam_ferree/avoiding-exceptions-in-aspnet-core-lb4>

# 先决条件

为了从这篇文章中获得最大收益，如果你熟悉面向对象编程、C#(或其他 C 系列语言)以及 ASP.NET 核心和 MVC 架构的基础知识，那是最好的。

如果你对这些话题中的任何一个都不熟悉，但想了解更多，这里有一些很棒的 [pluralsight](https://www.pluralsight.com) 课程:

*   [面向对象编程和 C#](https://www.pluralsight.com/courses/object-oriented-programming-fundamentals-csharp)
*   [ASP.NET 核心和 MVC](https://www.pluralsight.com/courses/aspdotnet-core-2-0-mvc-application-visual-studio-2017)

如果你没有 pluralsight 会员资格，他们会提供 10 天的免费试用。如果你想听我的意见:我已经试过 Treehouse，Udemy 和其他公司，Pluralsight 是我的明显赢家。无论你是编程新手还是经验丰富的工程师，每月的费用都是值得的。

# 为什么我不喜欢处理异常

### 抛出异常就是不诚实

考虑下面的代码片段:

```
public decimal SquareRootOf(int x)
{
    if(x < 0)
        throw new ArgumentException("x is negative");

    // Calculate the square root
} 
```

Enter fullscreen mode Exit fullscreen mode

我们总是看到这样的代码，这并不是一种可怕的做法。验证传递给函数的数据是一种很好的防御性编程实践。但是这个函数撒谎了。它说它在传递一个`int`时会返回一个`decimal`，它*可能会*，但也可能会返回一个`ArgumentException`。有几种方法可以让这段代码变得更加诚实，但是一个合适的解决方法是更加诚实地对待*您可以接受什么类型的参数*。如果这个函数声明 x 的类型是`uint`，那么不需要检查，它可以诚实地说是的，`uint`总是返回`decimals`。

### 异常处理是一种控制执行流程的拙劣方法

考虑 ASP.NET 核心的以下控制器动作

```
[HttpDelete("{id}"]
public IActionResult DeletePost(int id)
{
    try
    {
        var post = _postRepository.GetPostById(id);

        var currentUser = _userService.GetCurrentUser();

        if(post.Author.Id == currentUser.Id)
        {
            _postRepository.Delete(post);
            return NoContent();
        }

        return Forbid();
    }
    catch (NotFoundException nfe)
    {
        return NotFound();
    }
    catch (AuthorizationException ae)
    {
        return Unauthorized();
    }
    catch
    {
        return StatusCodeResult(500);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来像是有弹性的代码。它检查授权，返回定制的异常，这些异常很好地映射到适当的 HTTP 结果。但是这看起来有点不协调，而且控制流主要是通过异常路由的。此外，我们在这里有一些验证逻辑，我更喜欢将我的控制器的单一职责局限于编排。

# 生活无异常处理

### 这不是一个关于铁路编程的帖子。

想象一下，如果对象诚实地告诉你它们可以返回几种不同的类型？如果一个存储库方法可以告诉你“我将返回一个`Post`或者一个`Exception`”会怎么样？那不是很好吗？

从函数式编程中借用了一个概念，叫做面向铁路的编程...但我不会在这里谈论它。就我个人而言，我以前走过这条路，一旦你正确地处理了异步控制器...可读性(IMHO)消失了，我怀疑 C#虽然多用途，但还不能完全支持与函数式编程紧密相关的范例。我更喜欢面向对象的方法。不过，我会把这个链接留给感兴趣的人。

*   [面向铁路的编程](https://fsharpforfunandprofit.com/rop/)

注意:这篇文章详细介绍了 F#中面向铁路的编程，这是一种比 C#更适合这个任务的函数式语言，但它解释了这个概念。关于如何在 C#中实现这一点的信息，Zoran Horvat 有[这个](https://www.pluralsight.com/courses/making-functional-csharp)很棒的课程(是的，在 pluralsight 上)是关于如何让你的 C#代码更具功能性，它还有一个面向铁路的编程模块。

### 一个旧的最爱，结果类

当我请求某个服务方法做某件事，或者为我获取某样东西时，我真的只有一个问题。奏效了吗？如果是这样，我已经得到了，我可以继续前进了。如果出现错误，我应该能够快速失败，并在链的更上游返回发生的任何错误(就像我们对异常所做的那样)

我在许多语言和框架中使用过一个旧模式，其中包装类保存数据和调用的成功/失败信息。

由于我们把自己限制在 ASP.NET 核心控制器的领域，我们得到了一个很大的突破。有效的东西可以返回我们想要的对象。失败的东西可以返回一个`ActionResult`。因为做实际工作的类比控制器更清楚到底哪里出错了，所以它可以返回正确的`ActionResult`。通过这种方式，控制器现在可以摆脱将异常映射到状态代码的工作，并可以完全专注于编排的单一职责。

让我们看一些代码，首先是 void 方法的结果实现

```
public abstract class Result
{
    public ActionResult ErrorResult { get; private set; }

    public bool Errored => ErrorResult != null;

    protected Result()
    {
        ErrorResult = null;
    }

    protected Result(ActionResult errorResult)
    {
        ErrorResult = errorResult;
    }

    private class Success : Result { }

    private class Error : Result
    {
        public Error(ActionResult errorResult) : base(errorResult) { }
    }

    public static Result WithoutError => new Success();

    public static implicit operator Result(ActionResult errorResult) => new Error(errorResult);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们在这里打开包装。

首先，我喜欢将结果类抽象化，因为我不希望人们使用构造函数。有一个静态工厂属性，还有一个隐式转换，我们稍后会谈到。

`Result`的一个字段是`ActionResult` *错误结果*。由于`ActionResult`实现`IActionResult`，ASP.NET 核心控制器*通常*返回`IActionResult`，我们可以取*出错*的任意`Result`，只返回它的 *ErrorResult* 属性。

然后我定义了`Result` : `Success`和`Error`的两个私有实现。我不希望任何用户真正了解这些类型，它们只是我需要实例化一个`Result`对象时使用的实现

接下来，我们有一种方法来指示 void 方法的成功运行，这是一个静态属性，它返回封装在`Result`类型后面的`Success`的新实例。

然后我们有一个从任何`ActionResult`到一个`Result`的隐式转换，它实例化了一个新的`Error`(再次，被封装)

那么我们可以通过下面的方式从“void”方法返回

```
 public Result DoSomething()
{
    // Do Something
    return Result.WithoutError;
}

public Result DoSomethingThatFails()
{
    // Do Something That Fails
    return new InternalServerError();
}

public Result DoSomethingThatWorks()
{
    // Do Some Stuff

    Result otherMethodResult = Other.MethodThatWorks();

    // Fail Fast
    if(otherMethodResult.Errored)
        return otherMethodResult.ErrorResult;

    // Do Some More Stuff

    return Result.WithoutError;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们看到了如何无错误地返回，返回`ActionResult`对象并让它们被编译器转换成`Result`，以及轻松地从其他`Result`类型返回错误。适用于命令，但是这不能从查询中返回数据。

### 拿回某物，有结果

那么应该返回一些数据给我的方法呢？我如何得到它？我们有另一个 Result 子类可以处理这个

```
public class Result<T> : Result
{
    public T Value { get; private set; }

    private Result(T value)
    {
        Value = value;
    }

    private Result(ActionResult errorResult)
        : base(errorResult) { }

    public static implicit operator Result<T>(T value) => new Result<T>(value);
    public static implicit operator Result<T>(ActionResult errorResult) =>
        new Result<T>(errorResult);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我为我们希望返回的数据添加了一个字段，并使该类通用化，这样我们就可以得到任何类型的结果。我将构造函数保持私有，因为我希望人们使用隐式转换。所以返回 Result 的方法既可以返回 T 类型的对象，也可以返回任何表示错误的`ActionResult`。

**旁注:**是的，你可以用返回`Result<ActionResult>`的方法创建一个对象...或者换句话说，总是返回一些`ActionResult`，对于成功和错误，这似乎有点令人困惑...而且它肯定不会与我们已经设置的构造函数和隐式转换一起使用...但是已经有了一个对象的抽象，它总是返回一个 HTTP 结果，不管动作是否成功。他们被称为控制器！对于一个控制器来说，对本质上是另一个控制器的对象进行查询可能是没有意义的...所以我认为返回`Result<ActionResult>`是一个相当刺鼻的代码气味。继续前进...

让我们再来看一下我们的删除 post 操作，在这里我们可以看到，控制器已经把它的职责减少到只不过是编排解决请求的流程，并且一旦该流程的某个部分返回错误，它就会很快失败。

```
[HttpDelete("{id}")]
public IActionResult DeletePost(int id)
{
    Result<User> getUser = _userService.GetCurrentUser();

    // The user might be unauthenticated, the user
    // service is now responsible for detecting that
    // logging it, responding with challenge etc.
    if (getUser.Errored)
        return getUser.ErrorResult

    Result deletion = _postRepository.DeletePost(id, getUser.Value);

    // Was the post not found? 
    // Was the user unauthorized?
    // Did we lose our database connection? 
    // We don't know, we don't care. 
    if(deletion.Errored)
        return deletion.ErrorResult;

    // All must have went well.
    return NoContent();
} 
```

Enter fullscreen mode Exit fullscreen mode

咻！这是一篇很长的帖子，谢谢你一直坚持到最后！如果你有任何问题或意见，请在这里留下，或者如果他们稍后来找你，请发微博给他们。喜欢这个主意吗？[把我叉在 GitHub 上](https://github.com/SamCoSystems/SafeResult)