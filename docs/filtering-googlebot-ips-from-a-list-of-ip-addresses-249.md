# 从 IP 地址列表中过滤 Googlebot IPs

> 原文：<https://dev.to/mert/filtering-googlebot-ips-from-a-list-of-ip-addresses-249>

嘿，开发人员来了！

我最近需要一个简单的脚本来从 IP 地址列表中过滤 Googlebot IPs，以便能够从访问日志中提取实际的 Googlebot 访问。

谢天谢地，谷歌提供了一种方法来确保访问者确实是谷歌机器人。

如果你有类似的需求，那就来吧:

```
#/bin/bash
#
# Performs reverse and forward DNS lookups to list Googlebot's IPs, given a list
# of IP addresses as a file. Useful for filtering access logs to find out actual
# Googlebot visits.
#
# An implementation of https://support.google.com/webmasters/answer/80553?hl=en

while IFS='' read -r IP_ADDRESS || [[ -n "$IP_ADDRESS" ]];
do IS_GOOGLEBOT=0
    REVERSE_LOOKUP="$(host $IP_ADDRESS)"
    echo "$REVERSE_LOOKUP" | grep -E "google.com.$|googlebot.com.$" > /dev/null && IS_GOOGLEBOT=1

    if [[ IS_GOOGLEBOT -eq 1 ]]; then FORWARD_LOOKUP="$(host $(echo "$REVERSE_LOOKUP" | cut -d " " -f 5) | cut -d " " -f 4)"
        if [[ "$FORWARD_LOOKUP" = "$IP_ADDRESS" ]];
        then echo $IP_ADDRESS
        fi
    fi
done < "$1" 
```

Enter fullscreen mode Exit fullscreen mode

您可以将其保存为类似于`filter-googlebot-ips.sh`的格式，并提供一个包含要过滤的 IP 地址列表的文件(每个地址占一行)，作为一个参数。像这样:

```
$ ./filter-googlebot-ips.sh access-log-ips.txt > googlebot-ips.txt 
```

Enter fullscreen mode Exit fullscreen mode

这将为每个 IP 地址执行反向和正向 DNS 查找，并将经过验证的 Googlebot IPs 打印到`STDOUT`，您可以像上面的例子一样将它写入一个文件。

希望它能帮助那里的人！🙌

如果你喜欢的话，这里有一个 GitHub 的要点。