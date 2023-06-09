# 与 Swaggervel v2.3 一起使用 Laravel Passport

> 原文：<https://dev.to/w0rddriven/laravel-passport-usage-with-swaggervel-v2-3-4789>

### 概述

我最近几乎所有的 Laravel 项目都在使用这个 Swaggervel 包。有几个实例被稍微定制了一下，以适应不同的认证方案，我只是简单地提到了使用 Laravel Passport。

我想强调几个方面，同时也提供一个示例项目作为一个有点固执己见的出发点。仅仅高亮部分包含了相当多的信息，但是这个例子应该在提交消息和成品中有足够的信息。

### 设置 Laravel 和 Laravel 护照

首先，我们运行`laravel new <project_name>`、`git init`并立即提交以标记我们的基本 Laravel 安装。我总是倾向于立即提交，而不是先进行定制，因为与基础安装相比，跟踪定制要容易得多。接下来，我们浏览一下[的拉勒维尔护照文件](https://laravel.com/docs/5.6/passport)，并附上以下警告:

*   `php artisan vendor:publish --tag=passport-migrations`没有按预期复制迁移。我们手动这样做。
*   `php artisan migrate --step`为每个迁移文件单独创建一个迁移批次。这让我们可以回滚到单个步骤，这主要是个人偏好。
*   `app/Providers/AuthServiceProvider`包含以下内容:

```
Passport::routes(function (RouteRegistrar $routeRegistrar) {
    $routeRegistrar->all();
});
Passport::tokensCan([
]);
Passport::enableImplicitGrant();
Passport::tokensExpireIn(Carbon::now()->addDays(15));
Passport::refreshTokensExpireIn(Carbon::now()->addDays(30)); 
```

*   运行`artisan make:auth`以利用应用程序布局，并创建一个受登录提示保护的`home`视图。
    *   Passport Vue 组件可以显示在欢迎页面上，但我们正在尝试为未来的用户提供更好的实践。
*   创建一个合适的`WelcomeController`与利用相同应用程序布局的匹配视图
    *   这不是必须的，但是这一步使得在将来正确利用`artisan route:cache`成为可能，因为不支持路线关闭。

### 设置摇摆级别

现在基础已经完成，我们通过`composer require appointer/swaggervel --dev`引入 Swaggervel。我们可以忽略文档中提到添加`Appointer\Swaggervel\SwaggervelServiceProvider::class`的那一行，因为这仅适用于没有包发现的 5.5 之前的 Laravel 版本。有必要运行`artisan vendor:publish`来发布内容，因为我们使用这个包作为开发依赖，否则资产不会显示。既然 Swaggervel 已经就位，我们就可以把它集合起来。

首先，我们创建文件`app/Http/Controllers/Api/v1/Controller.php`作为通用 API 基本控制器。这个控制器在一个方便的位置存放了我们的根级`@SWG\Info`定义。这也为我们将来的 API 控制器版本化工作做好了准备，尽管这是个人偏好。秘制酱就是`@SWG\SecurityScheme`注解:

```
/**
 * @SWG\SecurityScheme(
 * securityDefinition="passport-swaggervel_auth",
 * description="OAuth2 grant provided by Laravel Passport",
 * type="oauth2",
 * authorizationUrl="/oauth/authorize",
 * tokenUrl="/oauth/token",
 * flow="accessCode",
 * scopes={
 * *
 * }
 * ),
 */ 
```

`securityDefinition`属性是任意的，但是需要包含在每个受保护的路由定义中。您可以指定多种安全方案来涵盖诸如一个通用 api 密匙或多个 OAuth 流之类的东西，尽管我还没有尝试过后者。[这些是受支持的流](https://swagger.io/specification/#oauthFlowsObject)，需要注意的是，Swaggervel 目前在`OpenAPI 2.0`规范中，尽管将来可能会改变。指定的`scopes`包括所有(*)，但是我们可以明确定义任何范围。应该注意的是，我们还需要在我们的资源控制器类中设置路由定义，但是由于过于冗长，它们无法包含在本文中。下面是使用这个设置所特有的一小段代码:

```
* security={
* {
* "passport-swaggervel_auth": {"*"}
* }
* }, 
```

这告诉一个特定的端点使用之前创建的`securityDefinition`,它们匹配是很重要的。示例项目有基本的`UserController`、`User`模型和`UserRequest`定义，这应该是一个不错的起点，尽管我不能保证它们非常全面。

### 配置我们的 OAuth 客户端

首先，我们需要专门为 Swaggervel 连接创建一个 OAuth 客户端。转到`/home`端点，在`OAuth Clients`下点击`Create New Client`。在`Name`下指定`Laravel Passport Swaggervel`或仅指定`Swaggervel`。在`Redirect URL`下，我们不能直接指定`/vendor/swaggervel/oauth2-redirect.html`，所以使用类似`https://passport-swaggervel.test/vendor/swaggervel/oauth2-redirect.html`的占位符。使用您选择的 SQL 工具箱，导航到表`oauth_clients`并查找具有上一步中指定的名称的行，在我们的例子中是`Laravel Passport Swaggervel`。手动将重定向列更新为`/vendor/swaggervel/oauth2-redirect.html`。

既然我们在 Passport 中的 OAuth 客户端应该设置正确，我们就把注意力放在`config/swaggervel.php`设置上。应该将`client-id`设置为 Passport 在 UI 中显示的`Client ID`字段。这也是`oauth_clients`表中行的 id。`client-secret`应该设置为 Passport 在 UI 中显示的内容，作为`Secret`字段。我们还将`secure-protocol`和`init-o-auth`都设置为 true，后者用我们的秘密填充 UI，否则我们必须手动输入它们。

### 修正 Swagger UI 捕捉令牌

为了让 OAuth2 重定向正常工作，我们需要修改`resources/views/vendor/swaggervel/index.blade.php`中的 Swagger UI 配置。在 url 参数的右下方的`const ui = SwaggerUIBundle({`下面应该是`oauth2RedirectUrl: '/vendor/swaggervel/oauth2-redirect.html',`。这种增强是必要的，因为没有这种增强，Swagger UI 就不能正确地“捕捉”标记。其他值得注意的增加使得用户界面更容易使用:

```
tagsSorter: 'alpha',
operationsSorter: 'alpha',
docExpansion: 'list',
filter: true 
```

### 通过 Swagger UI 测试认证

首先，我们转到`api/docs`端点来显示 Swagger UI。点击带有解锁挂锁图标的`Authorize`按钮。验证`client_id`和`client_secret`部分是否已填写。点击`Authorize`，标签为`Authorization Request`的 Laravel Passport 屏幕将显示`Authorize`和`Cancel`按钮。再次点击`Authorize`，你应该被重定向回 Swagger，此时`client_id`和`client_secret`显示为`******`，带有一个`Logout`按钮，而不是`Authorize`。我们现在应该能够点击`GET /users`路线，点击`Try it out`按钮，点击蓝色的`Execute`按钮，并以用户列表的形式收到我们预期的响应。

### 结论

我们满怀希望地强调了该过程的基本接触点，并通过示例代码进行了更详细的描述。该项目有点固执己见，以促进迄今为止对我很有用的实践。这绝不是一个完整的参考，但当没有一个全面的例子就很难看到全局时，这应该是一个很好的起点。

万一你需要[再次链接到项目](https://github.com/w0rd-driven/laravel-passport-swaggervel-example)。