# 使用 Blazor 和数据注释进行客户端验证

> 原文：<https://dev.to/rembou1/client-side-validation-with-blazor-and-data-annotations-1jgi>

# 用 Blazor 和系统进行客户端验证。数据注释

虽然我不太喜欢客户端验证(因为无论如何你都必须在服务器端进行验证)，但总有需要在表单中添加一些验证时使用客户端框架的时候，在客户端进行最简单的验证可以帮助你节省一些宝贵的服务器 CPU 时间。此外，我不喜欢客户端验证，因为大多数时候它会加倍所需的工作:您将使用您的服务器端技术(如 ASPNET Core)和客户端技术(比如 Knockoutjs)来编码规则，这意味着您必须保持两种代码同步，并且您必须能够在两端做相同的事情(并且您可能会面临困难，如处理 regex 的不同实现)。

有了 Blazor(以及所有下一代基于 web 组装的框架),这个问题消失了:后端服务可用的一切都可以用于前端应用。不是 Blazor 的所有情况，而是针对 netstandard 2 的所有情况。而我们足够幸运，这就是体制的情况。ComponentModel.DataAnnotation 是开源的，可以在这里获得:[https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src)。

我可以使用像 Fluent Validation 这样的其他验证 API，但是这个 API 很简单，很好地集成到了微软的 toolbelt (EFCore，ASPNET Core MVC)中，在我看来，这就足够了。

以下产品已经上市:

*   用于指定约束的属性(您也可以创建自己的属性)
*   根据实例验证约束的实用程序类
*   用于编写错误消息的 Api

但是对于前端部分，我们仍然需要做一些工作:

*   显示验证错误
*   发生验证错误时阻止表单提交

## 为实例调用验证

第一步将是定义我们的模型，这里是从我的项目注册模型[折腾](https://github.com/RemiBou/Toss.Blazor)

```
public class RegisterCommand : IRequest<CommandResult>
{
    [Required]
    [EmailAddress]
    [Display(Name = "Email")]
    public string Email { get; set; }

    [Required]
    [StringLength(100, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
    public string Name { get; set; }

    [Required]
    [StringLength(100,ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
    [DataType(DataType.Password)]
    [Display(Name = "Password")]
    public string Password { get; set; }

    [DataType(DataType.Password)]
    [Display(Name = "Confirm password")]
    [Compare("Password", ErrorMessage = "The password and confirmation password do not match.")]
    public string ConfirmPassword { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   验证已经在这里了，因为我已经做了服务器端验证，它是在我的 ApiController 上自动调用的

然后我创建一个调用验证的服务。因为我们是在一个 GUI 应用程序中(其中对象是长期存在的)，所以我使用 C#事件来传播验证错误。我做出这个选择是为了让开发人员的体验更容易:他不必管理验证器的创建，只需在正确的位置添加标签。

```
 /// <summary>
/// Validate a given instance
/// </summary>
/// <summary>
/// Validate a given instance
/// </summary>
public class ModelValidator : IModelValidator
{
    /// <summary>
    /// Raised is a validation error occurs
    /// </summary>
    public event EventHandler<ValidationErrorEventArgs> OnValidationDone;

    /// <summary>
    /// Validate the instance, if an error occurs returns false and raise the event
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="instance"></param>
    /// <returns></returns>
    public bool Validate(object instance)
    {
        List<ValidationResult> res = new List<ValidationResult>();
        var isValid = Validator.TryValidateObject(instance, new ValidationContext(instance, null, null), res, true);

        OnValidationDone?.Invoke(this, new ValidationErrorEventArgs() { Errors = res, Instance = instance });
        return isValid;

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我在 Program.cs
中将我的类作为单例注入

```
configure.Add(new ServiceDescriptor(
    typeof(IModelValidator),
    typeof(ModelValidator),
    ServiceLifetime.Singleton)); 
```

Enter fullscreen mode Exit fullscreen mode

## 阻止表单提交

第一步是在模型无效时阻止表单提交。为此，我将创建一个名为“ValidatedForm”的定制组件，子内容如下:

```
@inject IModelValidator ModelValidator
<form onsubmit="@OnSubmitWithValidate">
    @ChildContent
</form>
@functions {
    [Parameter]
    private RenderFragment ChildContent { get; set; }

    [Parameter]
    private Func<UIEventArgs,Task> OnSubmit { get; set; }

    [Parameter]
    private object Model { get; set; }

    private async Task OnSubmitWithValidate(UIEventArgs eventArgs)
    {
        if (ModelValidator.Validate(Model))
            await OnSubmit(eventArgs);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我可以添加更多的参数来设置表单标签的不同属性，但是为了简单起见，我将保持这种方式

我这样称呼它:

```
 <ValidatedForm OnSubmit="CreateAccount" Model="registerCommand">
 //....
 </ValidatedForm> 
```

Enter fullscreen mode Exit fullscreen mode

*   registerCommand 是我的页面上保存表单数据的私有字段
*   仅当 registerCommand 有效时，才会调用 CreateAccount
*   在表单中，我可以放入任何我想要的东西，包括 Blazor 组件

现在，如果我提交我的表单，什么也没有提交，但是我没有告诉用户任何事情。所以现在我必须显示一些友好的错误消息。

## 显示验证错误信息

为了显示验证错误消息，我将使用组件中的验证器发出的事件“ClientValidationError”:

```
@implements IDisposable
@inject IModelValidator modelValidator;
@if (!string.IsNullOrEmpty(errorMessage))
{
    <span class="small form-text text-danger">@errorMessage</span>

}
@functions{
    [Parameter]
    string FieldName { get; set; }
    [Parameter]
    object Model { get; set; }

    protected string errorMessage;
    protected override void OnInit()
    {
        modelValidator.OnValidationDone += HandleError;
        base.OnInit();
    }

    protected void HandleError(object sender, ValidationErrorEventArgs args)
    {
        if (args.Instance != Model)
            return;
        errorMessage = null;
        var error = args.Errors?.FirstOrDefault(e => e.MemberNames.Any(m => m == FieldName));
        if (error == null)
            return;
        errorMessage = error.ErrorMessage;

    }
    public void Dispose()
    {
        modelValidator.OnValidationDone -= HandleError;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   它实现了 IDisposable 来从事件中注销，所以浏览器不会在内存中保存所有已经显示的错误消息
*   我们在这里看到了为什么我在 ValidationErrorEventArgs 中发送实例。这里的要点是，如果我在同一个页面上有多个具有相同字段名的表单，要进行管理。
*   我没有把它和我的 I18n 集成在一起，我可能以后会这样做
*   字段名称将不得不由开发者手动输入，我现在不支持表达式
*   我还可以创建一个 ValidationSummary 来显示给定实例的所有错误

我在你之前看到的表单中添加了这样一个验证错误

```
 <ClientValidationError FieldName="Email" Model="registerCommand" /> 
```

Enter fullscreen mode Exit fullscreen mode

它只是工作，显示错误消息:)我不需要做一行验证代码，因为它已经使用了微软团队完成的所有东西(这是 web 组装框架的要点:跨客户端和服务器重用代码)。

## 结论

连接 Blazor 和 DataAnnotation 并不难，Blazor 的所有现有部分已经足够完成所有这些工作。也许开发人员使用 Linq Expression 会有更好的体验，我们可以尝试为无效文本框添加 css 类。

还有一个值得注意的限制:如果您实现的自定义验证属性需要像数据库一样访问服务器资源，那么它将会失败。但是对于这些验证，我不认为用 DataAnnotation 来管理它是一个好主意，有时一个简单的“如果”就足够了。

## 引用

*   [https://blazor . net/docs/components/index . html # component-dispose-with-idisposable](https://blazor.net/docs/components/index.html#component-disposal-with-idisposable)
*   [https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src)
*   [https://blazor.net/docs/components/index.html#child-content](https://blazor.net/docs/components/index.html#child-content)