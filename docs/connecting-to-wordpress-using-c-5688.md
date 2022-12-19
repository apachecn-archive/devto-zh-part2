# 使用 C#连接到 WordPress

> 原文：<https://dev.to/yeisonpx/connecting-to-wordpress-using-c-5688>

[![alt text](../Images/beee15d17e05379126add8b8695b5f10.png "Connecting c# to Wordpress")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AfBP32ow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://yeisonpx.com/wp-content/uploads/2018/09/Screen-Shot-2018-09-18-at-9.09.51-AM-1024x558.png)

在这篇文章中，我们将看到如何使用 C#连接到 Wordpress 网站，并通过三个简单的步骤使用 WordpressPCL 库制作出版物。

Wordpress 是一个很棒的 CMS，它允许开发者和不需要很多软件开发技能的人创建功能强大的网站。因此，实际上是用来建立网站的最流行的工具之一。

这个 CMS 可以非常灵活地创建自定义功能，尽管它很可能像一个插件一样可以安装到网站上。Wordpress 有一个很大的开发者社区，他们对这个平台有丰富的经验，在这里你可以找到几乎所有问题的解决方案。

Wordpress 是用 PHP 开发的，所以如果你不懂这门语言，你可能会在学习过程中浪费时间，然后实现你想做的事情。

接下来，我们将看到如何用 c#配置一个 visual studio 项目来连接到一个使用 Wordpress 的网站，这是一个很好的解决方案。Net 开发人员没有很好的 PHP 知识。

## 1。在你的网站中配置 Wordpress API

首先，你需要配置你的站点来启用允许在 WordPress 上读写的 API。使用 WordpressPCL 连接到 Wordpress 的先决条件是在您的网站上安装以下插件:

*   [WP REST API](https://github.com/WP-API/WP-API)
*   [WP REST API 的 JWT 认证](https://wordpress.org/plugins/jwt-authentication-for-wp-rest-api/)

那么要完成德 JWT 的配置就必须对其进行一些修改。 **htaaccess** 文件:

*   首先，启用 HTTP 授权头，添加以下内容:

```
 RewriteEngine on
    RewriteCond %{HTTP:Authorization} ^(.*)
    RewriteRule ^(.*) - [E=HTTP_AUTHORIZATION:%1] 
```

*   然后启用 WPENGINE，在同一个。**htaccess**文件:

```
 SetEnvIf Authorization "(.*)" HTTP_AUTHORIZATION=$1 
```

JWT 需要一个**秘密密钥**来签署令牌。这个**秘密密钥**必须是唯一的，并且永不泄露。

要添加**秘密密钥**，请编辑您的 wp-config.php 文件，并添加一个名为**的新常量 JWT _ 验证 _ 秘密 _ 密钥。**

```
 define('JWT_AUTH_SECRET_KEY', 'your-top-secrect-key'); 
```

你可以从这里生成并使用一个字符串[https://api.wordpress.org/secret-key/1.1/salt/](https://api.wordpress.org/secret-key/1.1/salt/)

你可以在这里看到文档中的所有细节。

## 2。从 Nuget 包安装 WordpressPCL

现在你需要从 Visual Studio 安装名为 **WordpressPCL** 的 Wordpress nuget 包。要打开掘金包管理器，在 Visual Studio 中右击你的项目，然后点击**管理掘金包**。

[![alt text](../Images/3d2acf191fb9b7aa83250353953b1782.png "Install WordpressPCL from Nuget Packages")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h75z_AxX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://yeisonpx.com/wp-content/uploads/2018/09/nugget-packages-1024x576.png)

然后搜索 **WordPressPCL** 并右击**安装**。

[![alt text](../Images/ba753749574e8931df776a527f35f290.png "Installing WordpressPCL from nuget packages")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M0VvpePP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://yeisonpx.com/wp-content/uploads/2018/09/nugget-packages-1-1024x532.png)

您也可以使用 nuget packages 控制台插入以下命令来安装软件包:

```
Install-Package WordPressPCL -Version 1.5.0 
```

你能用 WordpressPCL 做什么？下表显示了您可以使用的支持方法。

[![alt text](../Images/eb84e97ef10c1602777b336f3072501c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A96XkctS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://yeisonpx.com/wp-content/uploads/2018/09/Screen-Shot-2018-09-18-at-3.16.49-PM.png)

现在我们已经配置并安装了你需要的所有东西，让我们看看如何从 Wordpress 创建、更新和查询数据。

### 示例 1:连接到 Wordpress

要连接到 Wordpress 客户端，你可以使用类 **WordPressClient** 来接受你网站的 URL。http://domain-example.com/wp-json/的例子[。其中 **/wp-jon/** 是 Wordpress REST API 的默认路径。](http://domain-example.com/wp-json/)

```
private static async Task<WordPressClient> GetClient()
        {
            // JWT authentication
            var client = new WordPressClient("http://wordpress-domain.com/wp-json/");
            client.AuthMethod = AuthMethod.JWT;
            await client.RequestJWToken("user", "password");
            return client;
        } 
```

### 例 2:创建和更新数据

```
using System;
using System.Linq;
using System.Threading.Tasks;
using WordPressPCL;
using WordPressPCL.Models;

namespace WordPressTest
{
    class Program
    {
        static void Main(string[] args)
        {
            CreatePost().Wait();
        }

        private static async Task CreatePost()
        {
            try
            {
                WordPressClient client = await GetClient();
                if (await client.IsValidJWToken())
                {
                    var post = new Post
                    {
                        Title = new Title("New post test"),
                        Content = new Content("Content for new post.")
                    };
                    await client.Posts.Create(post);
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.Message);
            }
        }

        private static async Task<WordPressClient> GetClient()
        {
            // JWT authentication
            var client = new WordPressClient("http://wordpress-domain.com/wp-json/");
            client.AuthMethod = AuthMethod.JWT;
            await client.RequestJWToken("user", "password");
            return client;
        }
    }
} 
```

### 例 3:获取数据

查询数据非常简单，如下图所示:

```
 var client = GetClient();

    //Getting all the post
    var posts =await client.Posts.GetAll();

    //Getting one post
     var onePost = await client.Posts.GetByID(1); 
```

你可以从我在项目 [WordpressTest](https://github.com/yeisonpx/WordpressTest) 中的 Github 账户下载这些例子，关于 WordpressPCL API 的更多信息，你可以查看这里的官方文档: [Github WordpressPCL 文档。](https://github.com/wp-net/WordPressPCL)

我希望我能帮助你很好地了解如何使用. Net 连接 Wordpress 网站。

如果你有男朋友，请告诉我。

原帖:[使用 C Sharp 一步步连接 Wordpress】](https://yeisonpx.com/software-develoment/a-step-by-step-to-connect-to-wordpress-using-c-sharp/)