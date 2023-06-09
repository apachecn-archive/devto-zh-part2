# 在 PHP 中从 STDIN 读取密码

> 原文：<https://dev.to/mykeels/reading-passwords-from-stdin-in-php-1np9>

大家好，

最近，我不得不创建一个代表用户提交一些数据的 [artisan 命令](https://laravel.com/docs/5.6/artisan)。

我需要它获得用户的密码才能做到这一点。

我首先想到的是将密码存储在一个环境变量中。我真的不想这么做，我更喜欢让脚本的用户在每次需要的时候输入密码。

[![](img/66badea3a8405ef504844d29ca14456e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qjqpxjtZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://cdn.osxdaily.com/wp-content/uploads/2015/02/typing-password-into-terminal.jpg)

<center><small><u>[Secure Password Prompt](http://osxdaily.com/2015/02/04/terminal-wont-show-password-when-typed)</u><small></small></small></center>

 <small>## 这是怎么做到的

确保安全地输入密码是很重要的(也是不可见的)，所以我研究了如何输入，下面是我想到的。

```
private function getPassword($prompt = "Enter Password:") {
    echo $prompt;

    system('stty -echo');

    $password = trim(fgets(STDIN));

    system('stty echo');

    return $password;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我是怎么理解的

我现在理解的`stty`命令[设置终端属性](https://www.thinkage.ca/gcos/expl/stty.html)。

*   `stty -echo`将`echo`属性设置为 false，这样终端就不会将接收到的输入回显到屏幕上，从而使密码不可见

*   `fgets(...)`从流接口(STDIN)获取输入。

*   `STDIN`是`fgets`从中读取输入的[输入流](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin))

*   `stty echo`将`echo`属性恢复为 true，因此我们现在可以再次在屏幕上看到输出

我希望你和我一样，从这件事中学到了一些东西。快乐编码。😍</small>