# 如何在 Windows 主机上用 Laravel Homestead 设置 HTTPS

> 原文：<https://dev.to/vintagesucks/how-to-setup-https-with-laravel-homestead-on-a-windows-host-odl>

本指南假定有流浪者和拉勒维尔家园的经验。

Laravel Homestead 是一个用于本地 Laravel 开发的易于使用的流浪者盒子，但是如果您以前从未做过，让 SSL 在 Windows 主机上工作可能会很棘手。本指南将向您展示如何在 Firefox 中启用 SSL 以及如何信任证书。

先决条件:

*   [作曲家](https://getcomposer.org/)
*   [火狐](https://www.mozilla.org/firefox/)
*   [流浪者](https://www.vagrantup.com/)

出于演示目的，让我们创建一个新的 Laravel 项目:

```
composer create-project --prefer-dist laravel/laravel ssl-example 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要为我们的项目配置一个 Homestead 实例:

```
cd ssl-example
composer require laravel/homestead --dev
vendor\\bin\\homestead make 
```

Enter fullscreen mode Exit fullscreen mode

您应该会在控制台中看到以下输出:

```
Homestead Installed! 
```

Enter fullscreen mode Exit fullscreen mode

编辑`Homestead.yaml`并进行以下更改:

```
-       map: homestead.test +       map: laravel.localhost
        to: /home/vagrant/code/public
+ ssl: true 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:请随意将整个指南中的`laravel.localhost`更改为您选择的本地域。

您的`Homestead.yaml`现在看起来应该类似于这个:

```
ip: 192.168.10.10
memory: 2048
cpus: 1
provider: virtualbox
authorize: ~/.ssh/id_rsa.pub
keys:
    - ~/.ssh/id_rsa
folders:
    -
        map: 'D:\Documents\GitHub\ssl-example'
        to: /home/vagrant/code
sites:
    -
        map: laravel.localhost
        to: /home/vagrant/code/public
databases:
    - homestead
name: ssl-example
hostname: ssl-example
ssl: true 
```

Enter fullscreen mode Exit fullscreen mode

在`C:\Windows\System32\drivers\etc\hosts`中添加以下一行:

> 注意:IP 地址应该与`Homestead.yaml`
> 匹配

```
192.168.10.10 laravel.localhost 
```

Enter fullscreen mode Exit fullscreen mode

安装家园流浪箱:

```
vagrant box add laravel/homestead 
```

Enter fullscreen mode Exit fullscreen mode

让我们开始我们的流浪盒子:

```
vagrant up 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用本指南向现有虚拟机添加 SSL，请使用以下命令:

```
vagrant reload --provision 
```

Enter fullscreen mode Exit fullscreen mode

请等待，直到设置过程完成。

现在，您应该能够在浏览器中访问 [http://laravel.localhost](http://laravel.localhost) :

[![Laravel over HTTP](img/c5f5610a7269e0676b412a5555db722c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xQisC0MP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nikol.as/img/2018/10/laravel-http.png)

现在我们需要从虚拟机获取 CA 证书:

```
vagrant ssh -c 'cat /etc/nginx/ssl/ca.homestead.ssl-example.crt' > ca.homestead.ssl-example.crt 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:更改`ssl-example`以匹配您的`Homestead.yaml`。

你现在应该在你的`ssl-example`文件夹中有`ca.homestead.ssl-example.crt`。

在 Powershell 中，以管理员身份运行以下命令，将证书添加到证书存储中:

```
certutil -addstore -enterprise -f "Root" ca.homestead.ssl-example.crt 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:现在您可以从`ssl-example`文件夹中移除`ca.homestead.ssl-example.crt`。

最后但同样重要的是，我们需要让 Firefox 信任这个证书。

在 Firefox 中，导航到`about:config`。如果你不熟悉火狐的配置编辑器，请阅读[这篇介绍](https://support.mozilla.org/kb/about-config-editor-firefox)。

将`security.enterprise_roots.enabled`设置为`true`并重启 Firefox。

现在，您应该能够在浏览器中访问[https://laravel . localhost](https://laravel.localhost)。

[![Laravel over HTTPS](img/2cd481998864e695cda4c30d9839904f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SecQ_QU9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nikol.as/img/2018/10/laravel-https.png)

恭喜你！🎉