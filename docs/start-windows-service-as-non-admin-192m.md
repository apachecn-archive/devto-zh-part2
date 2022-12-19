# 以非管理员身份启动 Windows 服务

> 原文：<https://dev.to/c33s/start-windows-service-as-non-admin-192m>

为 windows 服务设置权限真的很糟糕。

我只想允许非管理员启动和停止我的 php 工作程序:

```
sc sdshow php5-cgi-worker01
D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;RPWPDT;;;S-1-5-21-<snip>-<snip>-<snip>-1000) 
```

Enter fullscreen mode Exit fullscreen mode

可以用`sc sdset ...`设置权限

更简单的方法是使用 [SubInACL](https://www.microsoft.com/en-us/download/details.aspx?id=23510) 作为 admin:

```
subinacl.exe /service php5-cgi-worker01 /grant=username=PTO 
```

Enter fullscreen mode Exit fullscreen mode

(P =暂停，T =开始，O =停止)

然后，您可以使用您的用户启动服务:

```
sc start php5-cgi-worker01 
```

Enter fullscreen mode Exit fullscreen mode

*   [http://woshub.com/set-permissions-on-windows-service/](http://woshub.com/set-permissions-on-windows-service/)
*   [https://www.microsoft.com/en-us/download/confirmation.aspx?id=23510](https://www.microsoft.com/en-us/download/confirmation.aspx?id=23510)
*   封面图片作者 geralt[https://pix abay . com/en/binary-hands-keyboard-tap-enter-2372130/](https://pixabay.com/en/binary-hands-keyboard-tap-enter-2372130/)