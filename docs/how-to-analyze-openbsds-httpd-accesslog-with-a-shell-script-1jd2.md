# 如何用 shell 脚本分析 OpenBSD 的 httpd access.log

> 原文：<https://dev.to/mnlwldr/how-to-analyze-openbsds-httpd-accesslog-with-a-shell-script-1jd2>

我想要一个 shell 脚本来分析我来自 httpd 的访问日志。我从 [@ruanbekker](https://twitter.com/ruanbekker) 那里找到了[这个](https://sysadmins.co.za/bash-script-to-parse-and-analyze-nginx-access-logs/)，采用了它，并修改为 httpd。

你可以看到

*   按 IP 排列的顶级请求
*   请求像`GET`或`HEAD`这样的方法
*   响应代码，如`200`、`301`、`404`
*   热门请求页面，如`/atom.xml`
*   具有 404 响应的顶级请求页面

你可以在这里下载脚本

```
#!/bin/sh

LOGFILE="/var/www/logs/access.log"
RESPONSE_CODE="200"

filters() {
grep $RESPONSE_CODE \
| grep -v "<UNKNOWN>" \
| grep -v "favicon.ico" \
| grep -v "logfile turned over"
}

filter_response_codes()
{
grep -v "<UNKNOWN>" \
| grep -v "logfile turned over" \
| awk '{print $10}'
}

filter_404_response() {
grep "404"
}

ips() {
awk '{print $2}'
}

pages() {
awk '{print $8}'
}

domain() {
awk '{print $1}'
}

methods() {
awk '{print $7}' | cut -d'"' -f2
}

sort_count() {
sort | uniq -c
}

sort_desc() {
sort -rn
}

top_ten() {
head -10
}

sep() {
echo "=================================================="
}

##
# Actions
##
action_request_ips() {
    echo ""
    echo "Top requests from IPs"
    sep
    cat $LOGFILE \
        | filters \
        | ips \
        | sort_count \
        | sort_desc \
        | top_ten
    echo ""
}

action_request_methods() {
    echo ""
    echo "Count requests methods"
    sep
    cat $LOGFILE \
        | filters \
        | methods \
        | sort_count
    echo ""
}

action_pages() {
    echo ""
    echo "Top requested pages"
    sep
    cat $LOGFILE \
        | filters \
        | pages \
        | sort_count \
        | sort_desc \
        | top_ten
    echo ""
}

action_404() {
    echo ""
    echo "Top requests 404"
    sep
    cat $LOGFILE \
        | filter_404_response \
        | pages \
        | sort_count \
        | sort_desc \
        | top_ten
    echo ""
}

action_response_codes() {
    echo ""
    echo "Response code"
    sep
    cat $LOGFILE \
        | filter_response_codes \
        | sort_count \
        | sort_desc
    echo ""
}

action_request_ips
action_request_methods
action_response_codes
action_pages
action_404
```

## 输出

```
$ analyze_access_log

Top requests from IPs
==================================================
13 1.2.3.4
 8 1.2.3.5
 8 1.2.3.6
 4 1.2.3.7
 4 1.2.3.8
 2 1.2.3.9
 2 1.2.3.10
 2 1.2.3.11
 2 1.2.3.12
 2 1.2.3.13

Count requests methods
==================================================
1146 GET
  10 HEAD

Response code
==================================================
1190 200
 792 304
 615 301
  80 404
   8 400
   5 403
   1 405
   1 206
   1 0

Top requested pages
==================================================
 694 /atom.xml
 136 /
  92 /xxx
  46 /yyy
  23 /zzz
  20 /aaa
  17 /eee/fff
  16 /humans.txt
  14 /foo/bar
  12 /something/else

Top requests 404
==================================================
  61 /robots.txt
   5 /css_.php
   2 /admin
   2 /.git/config
   1 /wp-login.php?action=register
   1 /login
   1 /user/register
   1 /index.php?option=com_user%2526task=register
   1 /etc/passwd
   1 /console 
```

你可以在这里下载脚本