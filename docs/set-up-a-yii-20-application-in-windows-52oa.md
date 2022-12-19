# 在 Windows 中设置 Yii 2.0 应用程序

> 原文：<https://dev.to/ows_ali/set-up-a-yii-20-application-in-windows-52oa>

[![](../Images/154ef5126628ba5c47917809b3077b01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mSrwC8Ag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/650/0%2A0Ci9Hikv6--WF2Dq.jpg) 

<figcaption>PHP Yii 2.0 框架</figcaption>

让我们在本地 PC 上安装一个 Yii 2.0 应用程序。我们将在 Windows 上安装 Yii 2.0 高级应用程序以及:

*   与 MySQl 的连接
*   启用漂亮的 URL
*   虚拟主机(用于后端和前端)
*   正在安装管理主题

### 安装项目

让我们使用 composer 在我们的 PC 上安装应用程序(如果您还没有安装 Composer，应该先安装它)。

转到 xampp/htdocs 文件夹，在 cmd 中运行以下命令:

```
composer create-project --prefer-dist yiisoft/yii2-app-advanced ows-yii2-application 
```

该应用程序将安装在“ows-yii2-application”目录/文件夹中。您可以根据项目名称对其进行重命名

进入 cmd 中的“ows-yii2-application”文件夹，然后运行命令:

```
init 
```

选择“0”或“dev”在开发环境中初始化应用程序。

运行以下命令安装依赖项:

```
composer install 
```

现在，您已经在本地 PC 上拥有了工作应用程序。使用 XAMPP/WAMP 或您使用的任何其他服务器打开您的 Apache 服务器，并转向

> localhost/ows-yii 2-应用程序/后端/web

和

> localhost/ows-yii 2-应用程序/前端/web

### 数据库与 MySQL 的连接

在本地计算机上创建一个名为“ows_database”的数据库。要连接到这个数据库，请转到 common/config/main-local.php 并将 dbname 设置为

```
'dsn' => 'mysql:host=localhost;dbname=ows\_database',
'username' => 'root',
'password' => '' 
```

(如果您更改了用户名和密码，也请编辑它们，否则保持原样)

现在，运行命令

```
yii migrate 
```

这将在数据库中生成用户表和 RBAC 表，以供将来使用。

现在，去

[http://localhost/ows-yii 2-application/frontend/web/index . PHP？r =站点% 2f 注册](http://localhost/practice/ows-application/frontend/web/index.php?r=site%2Fsignup)

并创建一个用户

> ***用户名*** : *测试用户名*
> 
> ***密码*** : *测试密码*

现在，您可以使用这些凭证登录到应用程序。

### 启用漂亮的 URL

目前网址看起来是:

[http://localhost/ows-yii 2-application/back end/web/index . PHP？r =站点% 2 登录](http://localhost/ows-application/backend/web/index.php?r=site%2Flogin)

但我们希望它是这样的:

[http://localhost/ows-yii 2-application/back end/web/log in](http://localhost/ows-application/backend/web/login)

为了启用漂亮的 URL，请转到 backend\config\main.php 并取消注释$components ['urlManager'](do%20not%20forget%20to%20add%20the%20rules%20as%20well) 中的以下代码:

```
'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => [
                '<alias:\w+>' => 'site/<alias>',
            ],
        ], 
```

使用以下代码创建一个. htaccess 文件，并将其放在 backend/web 文件夹中:

```
RewriteEngine on

RewriteCond %{REQUEST\_FILENAME} !-f
RewriteCond %{REQUEST\_FILENAME} !-d
RewriteRule . index.php 
```

在前端应用程序中做同样的事情。

### 虚拟主机

现在，我们希望从两个不同且容易记住的域名/服务器名运行我们的后端和前端应用程序。比如:

admin.ows-yii2-application.com 用于后端应用程序

，ows-yii2-application.com 用于前端应用程序

。

为了实现这一点，请将以下内容复制并粘贴到您的 httpd-vhosts 文件中，很可能是在“xampp \ Apache \ conf \ extra \ httpd-vhosts”上(确保更改 DocumentRoot 以将其设置为您自己的 web 目录)

```
<VirtualHost \*:80>
       ServerName admin.ows-yii2-application.com
       DocumentRoot "E:\xampp\htdocs\ows-yii2-application\backend\web"
</VirtualHost>

<VirtualHost \*:80>
       ServerName ows-application.com
       DocumentRoot "E:\xampp\htdocs\ows-yii2-application\frontend\web"
</VirtualHost> 
```

以及位于 C:\ Windows \ System32 \ drivers \ etc 的 hosts 文件中的以下内容(您可能需要以管理员模式打开该文件):

```
127.0.0.1 admin.ows-yii2-application.com
127.0.0.1 ows-yii2-application.com 
```

重新启动 Apache 服务器。

现在，你可以分别在 admin.ows-yii2-application.com 和 ows-yii2-application.com 访问你的后端和前端应用程序。

### 在 Yii 2.0 应用程序中安装 AdminLTE 主题

现在让我们也为我们的应用程序设置 AdminLTE 主题。运行以下命令

```
composer require dmstr/yii2-adminlte-asset "^2.1" 
```

现在，转到 frontend/config/main.php 并将其添加到 components['view']中

```
'view' => [
            'theme' => [
                'pathMap' => [
                    '@app/views' => '@vendor/dmstr/yii2-adminlte-asset/example-views/yiisoft/yii2-app'
                 ],
             ],
        ], 
```

如果您也想在后端应用程序中启用 AdminLTE 主题，请遵循与后端应用程序类似的步骤。

### 结论

因此，我们已经成功地建立了 Yii 2.0 应用程序，这是一个很好的 PHP 框架。你可以在我的仓库里找到所有这些东西:

【https://github.com/ows-ali/ows-yii2-application T2】

如果你什么都不懂。此外，如果你是 Github 的新手，想要学习基础知识，那么可以读一读:

[循序渐进学习 Git 和 Github](https://dev.to/ows_ali/learn-git-and-github-step-by-step-536i-temp-slug-5321610)

感谢您的阅读。如果你喜欢这篇文章，请给它一个掌声。