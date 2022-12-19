# 如果你觉得用 Mac + Docker + Laravel 很慢，原因可能是 Laravel Debugbar

> 原文：<https://dev.to/acro5piano/if-you-feel-slow-with-mac--docker--laravel-the-reason-may-be-laravel-debugbar-2ad9>

我研究了为什么我的开发环境采用 Mac + Docker + Laravel。

我的结论是禁用`Laravel Debugbar`。它将应用速度提高了大约 3 倍**。**

[![image.png](../Images/9aedb7a8d7b1fc9d110a1cd92511c9c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vf91ed0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/103885/ac4d4abf-e6d7-b2a2-568a-27740cc76298.png)

响应时间以前是 2.6 秒，现在是 0.85 秒，大约快了 3 倍。

# 相当大的原因

Laravel Debugbar 根据请求在`storage/debugbar/`下创建一个日志文件，就像`Xc590ef0f5b368cf34373093291b95147.json` **一样。**

这是一个带有请求的日志。

```
~> find storage/debugbar -type f | wc -l
58 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，我们可以查看 app 的堆栈跟踪。

有很多中间件，所以文件非常大。
我们可以找到超过 **24000 行**的格式化 json。

```
~> cat storage/debugbar/X2e75040e69de9cae006253a48fd5276c.json | jq . | wc -l
24676 
```

Enter fullscreen mode Exit fullscreen mode

这次文件的大小大约是 **500KB** 。

```
~> ls -lh storage/debugbar/X2e75040e69de9cae006253a48fd5276c.json
-rw-r--r--  1 kazuya-mac  staff   508K  3 27 18:32 storage/debugbar/X2e75040e69de9cae006253a48fd5276c.json 
```

Enter fullscreen mode Exit fullscreen mode

众所周知，当我们在 Mac 上运行 Docker 时，写文件非常慢。
所以停止日志记录会让应用程序运行得更快。

# 待办事宜

我想使用 Laravel Debugbar 本身，所以我正在寻找提高 Laravel Debugbar 性能的方法。
我觉得下面的方式应该是候选。

*   通过 HTTP 转发日志
*   将日志输出目录设置为除`storage/logs`以外的目录。
*   不要将`storage/`安装为对接器`volume`(是否可以不部分安装对接器...？)

我们可能会使 Laravel 应用程序更快，除了带有日志输出目录的 Debugbar。

# 偏离主题

这是有用的。

```
#!/usr/bin/env fish

while true curl -H 'Accept-Encoding: gzip' -Ss -LI -o /dev/null -w \
        '%{http_code} in %{time_total} seconds, %{content_type} as %{size_download} \n' \
        $argv
    sleep 1
end 
```

Enter fullscreen mode Exit fullscreen mode