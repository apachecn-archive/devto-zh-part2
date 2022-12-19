# 在 php 中总是使用`/'作为目录分隔符

> 原文：<https://dev.to/c33s/always-use--as-directory-seperator-in-php-43l7>

作为一个长期的 windows 用户和 php 开发者，我只能说:**不要用`DIRECTORY_SEPARATOR`** ！它没有真正的好处，但确实可以打破东西。

使用`/`。几乎每次都是更好的选择。

[![yes it break things!](../Images/98f3a255eb6c2a55b106bc797f727501.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IUljVMue--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.imgflip.com/2ap1k1.jpg)

如果你在 windows 上使用解析为`\`的`DIRECTORY_SEPARATOR`，事情往往会出错。例如:`\`在 URL 中被转换为`%5C`。

```
http://example.com/media/cache/resolve/my_thumb/d/0/1%5Cfilename.jpg 
```

Enter fullscreen mode Exit fullscreen mode

如果使用`DIRECTORY_SEPARATOR`,很容易发生(相对)路径(在 windows 中将包含反斜杠)被传递给应该是 url 的东西。假设大多数 php 开发人员使用的是 linux 或 mac(我个人不认识很多在 windows 上开发 php 的人)，他们不会注意到在 windows 环境中，一个反斜杠被传递给 url，并被转义为`%5C`，这导致了上面例子中的 URL。

windows 和 windows 上的 php 能够打开路径为`foo/bar\example/test.txt`
的文件，所以`DIRECTORY_SEPARATOR`没有实际用途，除了容易检测到 windows，或者例如，如果您导出文件列表的路径，windows 用户会混淆看到的是`/`而不是`\`

```
λ cat ./foo/bar\example/test.txt
mycontent 
```

Enter fullscreen mode Exit fullscreen mode

(是`cat`存在于 windows 上)

```
notepad ./foo/bar\example/test.txt 
```

Enter fullscreen mode Exit fullscreen mode

也会打开文件

`foo.php` :

```
<?php
$content = file_get_contents('foo/bar\example/test.txt');
var_dump($content); 
```

Enter fullscreen mode Exit fullscreen mode

```
λ php foo.php
C:\test\foo.php:5:
string(9) "mycontent" 
```

Enter fullscreen mode Exit fullscreen mode

甚至那也管用:

```
<?php
$content = file_get_contents('c:/foo/bar\example/test.txt');
var_dump($content); 
```

Enter fullscreen mode Exit fullscreen mode

*   适用于 windows 的卡特彼勒随适用于 windows 的 git 一起提供[https://git-scm.com/download/win](https://git-scm.com/download/win)
*   `λ`来自我使用的 shell。[http://cmder.net/](http://cmder.net/)
*   迷因链接自[https://imgflip.com/](https://imgflip.com/)
*   封面图片由 wezlo[https://www . pexels . com/photo/caution-dead-end-post-safety-237189/](https://www.pexels.com/photo/caution-dead-end-post-safety-237189/)【https://pixabay.com/en/sign-street-dead-end-1749093/】T2