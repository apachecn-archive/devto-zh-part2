# 如何在 Windows 上部署 Git

> 原文：<https://dev.to/programmingmonky/windowsgit-4eh0>

介绍在 Windows 环境中安装 Git。

笔者的环境是

*   windows 7 32 位です。

首先

[https://git-scm.com/downloads](https://git-scm.com/downloads)

访问，

为 windows 选择一个构件

[![windows](img/29b22c774d53dd03b97ad44650ca6dd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nRLE5w11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kgatgdt30lpb45tmauw6.PNG)

下载开始，一结束就点击安装程序。

在安装程序的初始画面中会出现 GPI 的同意画面

[![GPL](img/9823de541a35eb5e4fc9e9e410c940f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Smtk_y4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i90qu2ba5qol5zejw0l4.PNG)

仔细阅读，然后单击“下一步”

选择下载正下方的文件夹。

这次定为 c 正下方。

[![git](img/50ed5c294dd92c8b284eb0dd728aaab5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5DZJ9rit--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0prcqrfy544i5zruz57.PNG)

按“Next”按钮。

选择要下载的组件。

[![a](img/4edcc3e8ea7162baa426c7a2bbf8f9a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AzxCUFI_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bu2vz5eox9o3hxluif9h.PNG)

先用默认的东西进行。 “按下 Nex 按钮。 ”

我问你是否要制作开始菜单。

[![スタートメニュー](img/342f18f92e556feb06329ad7413705cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MGH3SnBV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4del67ox2ejhmdlb3qja.PNG)

笔者暂且就这样进行，不需要的人请去掉。 “单击下一步按钮”

选择 giteditor 的画面。 默认为 vim，

初学者很难使用 vim，所以暂时改为 nano。

※当然，喜欢 vim 的人请选择 vim。

[![vim](img/5cd18b20078e24d93d81ce51eff0af05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9q723PDm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10cd0o396dqe3irivzxv.PNG)

持有 notepad++的人选择那个也说不定不错。

选择换行符的处理方式。

[![改行](img/299d3048c407b82a3782358614f9e1c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ULDyLWVz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cjkt4kb6fmhe3s8bobxv.PNG)

暂且保持默认状态进行。

我去问一下 TLS 的库。

[![TLS](img/db76183d0e149ebfb0a8de3974b38608.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--26QWcXZ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1s6tswxcls1gqe8r2d1.PNG)

选择默认的打开 SSL，然后单击下一步

我来问一下换行的转换。

[![kaigyou](img/3662ab82fc56b47a4d766e6eaede8afd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d-P-Rqxc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5b8ymx9mzlk2xnc1hxho.PNG)

总之，因为不会进行跨平台的开发，所以选择

Windows 环境推荐的最上面一个吧。

我去问一下要使用的控制台。

[![console](img/182db72ef132337891b69806d5019bee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OlgqUu8a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwdoa0h92q9gysb3u2en.png)

这次不使用 cygwin 等，所以使用 windows 的 cmd。

选择下面的复选框，然后单击" Next "

配置文件的设置

[![conf](img/f2ed127fe0077b1a3f893d6ce684ad0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l8joSOuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uswpn5cc9v28zml759ce.png)
保留默认值，按“Install”

等一会儿，Git 就安装好了。

[![git](img/abd3caacc0b5040c4b98c76bd7513966.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SYsOTN94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39lxtu0l04181vdjxq32.png)