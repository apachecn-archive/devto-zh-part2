# 如果用 golang 的 echo 建设静态网站的话，Now 很简单

> 原文：<https://dev.to/fk2000/golangechonow-3kic>

我们发现名为 Now 的 Paas 很容易部署 golang。

> 用 golang echo 试着用服务器写 Dockerfile 用 now 上传。 随便取了一个域，在 now ln 上贴了 alias。 非常简单，还会给我做 LE。 方便屋。 [https://t.co/fXT7yR2BZC](https://t.co/fXT7yR2BZC)
> 
> — mattn (@mattn_jp) [2018 年 9 月 18 日](https://twitter.com/mattn_jp/status/1042078292944093184?ref_src=twsrc%5Etfw)

## 公开之前的步骤

*   创建 Now 帐户
*   通过 DotTk 获取免费域。
    *   在 Nameserver 上指定 4 个 zeit.world 左右。
*   now.jsonを美因戈码头区作成する。
    *   将获得的域记述在 now.json 的 alias 中。
*   确认能否在 now 中暂时部署。
*   如果没有问题的话，在 now alias 上部署为 production。

## 源代码

[这边](https://github.com/fk2000/docker-go-now)