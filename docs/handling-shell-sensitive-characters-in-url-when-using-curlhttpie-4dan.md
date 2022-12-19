# TIL:使用 curl/httpie 时处理 URL 中的外壳敏感字符

> 原文：<https://dev.to/jonasbn/handling-shell-sensitive-characters-in-url-when-using-curlhttpie-4dan>

# 使用 curl/httpie 时处理 URL 中的外壳敏感字符

有时您想从命令行请求一个 URL，但是该 URL 包含在您的 shell 中有意义的字符。

```
$ curl --header Accept:application/json https://USERID:funkyPassMo|)@eksempel.dk/dostuff
-bash: syntax error near unexpected token '|' 
```

Enter fullscreen mode Exit fullscreen mode

使用 curl 时简单地引用 url

```
$ curl --header Accept:application/json \ 
"https://USERID:funkyPassMo|)@eksempel.dk/dostuff" 
```

Enter fullscreen mode Exit fullscreen mode

如果你的特殊字符是一个引号`"`，引用不同，使用`'`为例

```
$ curl --header Accept:application/json \
'https://USERID:funky"PassMo|)@eksempel.dk/dostuff' 
```

Enter fullscreen mode Exit fullscreen mode

参见:[http://wiki.bash-hackers.org/syntax/quoting](http://wiki.bash-hackers.org/syntax/quoting)

最初发布于[我的 TIL 收藏](https://jonasbn.github.io/til/)