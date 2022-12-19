# 在 Laravel 代客环境变量中运行旧站点

> 原文：<https://dev.to/lloople/running-old-sites-in-laravel-valet---environment-variables-4k96>

几天前，我的 Homestead 机器决定死机，无法再次启动(我完全确定我弄坏了什么东西， [Joe P Ferguson](https://twitter.com/JoePFerguson) 用那台流浪汉机器做了一件惊人的工作，在[流浪汉之家网站](https://app.vagrantup.com/boxes/search)上下载量排名第二😱)

事情是这样的，我已经在我的 Macbook 上安装了 [Laravel Valet](https://laravel.com/docs/5.6/valet) ，所以我想这次我可以在那台机器上运行这个项目。

但是旧站点通常是在考虑 Apache 服务器的情况下创建的，它们使用环境变量和 htaccess(我将在另一篇文章中谈到这一点)。

由于 Valet 目前不支持自定义的`.env`文件，我找到了一些加载变量的自定义方法，但是所有这些方法都弄乱了代码，我不想在提交到存储库之前从`index.php`中删除这些行。太冒险了。

但是检查这个 [pull 请求](https://github.com/laravel/valet/pull/474)的内容时，我想到了一个类似的方法，不涉及 nginx 配置。

由于[官方文档建议](https://laravel.com/docs/5.6/valet#custom-valet-drivers)，我在 app 目录下创建了一个名为`LocalValetDriver.php`的文件，内容如下

```
class LocalValetDriver extends LaravelValetDriver
{
    /**
     * Determine if the driver serves the request.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return bool
     */
    public function serves($sitePath, $siteName, $uri)
    {
        return true;
    }

    /**
     * Get the fully resolved path to the application's front controller.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string
     */
    public function frontControllerPath($sitePath, $siteName, $uri)
    {
        return $sitePath.'/public/index.php';
    }
} 
```

然后在`frontControllerPath`中，我加载环境变量:

```
public function frontControllerPath($sitePath, $siteName, $uri)
{
    $this->loadEnvironmentVariables();

    return $sitePath.'/public/index.php';
}

private function loadEnvironmentVariables()
{
    // all env variables here
    putenv('VAR=value');
    ...

} 
```

### 忽略存储库中的文件(可选)

剩下的唯一操作是将`LocalValetDriver`添加到您的全局`gitignore`中。此操作将阻止此包含敏感信息(也与项目无关)的文件上传到存储库。

如果您的主目录中没有名为`.gitignore_global`的文件，您将需要首先运行这个命令来将它与您的 git 配置
链接起来

```
git config --global core.excludesfile ~/.gitignore_global 
```

将`LocalValetDriver.php`添加到该文件

```
echo "LocalValetDriver.php" >> ~/.gitignore_global 
```