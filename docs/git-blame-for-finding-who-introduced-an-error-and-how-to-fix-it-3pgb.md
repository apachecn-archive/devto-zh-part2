# 找出谁引入了一个错误以及如何修复它。

> 原文：<https://dev.to/levivm/git-blame-for-finding-who-introduced-an-error-and-how-to-fix-it-3pgb>

正如我们所知，有时，有人可以添加/删除一些东西，之后项目开始到处出现错误。

第一个行动是找到错误，然后如果你的团队有三个以上的开发人员，找出是谁引入了错误(通过检查提交)可能会很痛苦，这是第二个行动。知道是谁干的。幸运的是，我们可以使用一个不知道(对于初学者)的命令`git blame`。

基本上，我们执行`git blame path_of_the_file_where_the_error_is`，会出现一个交互式提示，逐行显示我们的文件，以及提交和编写/编辑该行代码的人。

示例:

我们发现有人无意中更改了生产文件的设置，允许来自任何主机的 CORS 请求。在我们的平台上引入了一个巨大的安全漏洞。

我们知道对我们的生产设置文件进行了更改，因此，我们运行*git debit*

```
git blame settings/production.py
>>
......
72a82b25 (Juan Manuel 2018-03-27 08:46:42 -0500 30) DEBUG = False
72a82b25 (Juan Manuel 2018-03-27 08:46:42 -0500 31)
c54eabe6 (Levi Velazquez 2018-04-12 19:58:38 -0500 32) CORS_ORIGIN_ALLOW_ALL = True
...... 
```

Enter fullscreen mode Exit fullscreen mode

上面写着，在提交的`c54eabe6` Levi Velázquez(就是我)-日期:2018-04-12 19:58:38，他编辑/添加了这一行`CORS_ORIGIN_ALLOW_ALL = True`

所以，如果我们想知道它在改变之前是什么，为了修复它。我们使用 git show 使用我们的提交名

```
git show c54eabe6
>>
commit c54eabe67350229f3977d9f268eb638f954cf5ea
Author: Levi Velazquez <levinoelvm@gmail.com>
Date:   Thu Apr 12 19:58:38 2018 -0500

    allowed CORS to all host

- CORS_ORIGIN_WHITELIST = [
-    'host1.com',
-    '.companydomain.com',
- ]
+ CORS_ORIGIN_ALLOW_ALL = True 
```

Enter fullscreen mode Exit fullscreen mode

然后**瞧**，我们可以看到提交日期、作者、消息，以及添加和删除的行。据此，Levi(me)删除了`CORS_ORIGIN_WHITELIST`变量，并将`CORS_ORIGIN_ALLOW_ALL`设置为`True`，允许来自任何主机的所有 CORS 请求。

然后，我们可以恢复我们的更改并修复错误。

总之，责怪我或喜欢得到的风格:羞耻*羞耻*羞耻。
[![shame](img/a581c3041f3d75371c25325d5e0933cb.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--M0tA-yE8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/prv1y7odzrpvqp1av1x8.jpg)

声明:非生产环境在本帖创作过程中受到伤害。