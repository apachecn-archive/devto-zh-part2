# 在 Centos 7 中使用 Certbot 安装 SSL 证书

> 原文：<https://dev.to/ab0nilla/install-ssl-certificate-with-certbot-in-centos-7-47b2>

我是运行 CENTOS 7 的 HP ML110 Gen9 的所有者，拥有 ccTLD。首先你需要一个配置好的服务:named，httpd，firewalld。我不知道你是否需要相同域的电子邮件来获得证书，但配置后缀和 dovecot。

## 要求 1:

您需要一个虚拟主机，因为 certbot 会使用您的“domain . conf”(/etc/httpd/conf . d/domain . conf)将配置与该文件进行比较。

## 要求 2:

### 首先安装 epel-release。

yum 安装 epel-release。

### -我不记得你是否需要实用程序，但去安装。

yum 安装 yum-utils

## 安装 Certbot

### install certbot

yum 安装 cerbot-Apache

### -运行 certbot

certbot -阿帕奇语

### 设置地狱犬

-这是直观的配置，电子邮件通知，键入您的域名:domain.com.sv，[www.domain.com.sv](http://www.domain.com.sv)和 cerbot 为您的网站配置重定向；不要跳过这一步！！。

## 要求 3:

在我的情况下，certbot 没有很好地重定向，也许你需要添加这一行。htaccess

重写引擎开启
重写秒% { HTTP _ HOST }<sup>domain.com.sv</sup>【NC】
重写秒%{HTTPS}关闭
重写器<sup>。*</sup>$ https://% { HTTP _ HOST } % { REQUEST _ URI }

重写秒% { HTTP _ HOST }<sup>domain.com.sv</sup>【NC】
重写秒%{HTTPS}关
重写器<sup>。*</sup>$ https://% { HTTP _ HOST } % { REQUEST _ URI }

## 要求 4:

服务 httpd 重新启动

我仍然没有配置自动更新；也许你已经知道我来自萨尔瓦多，我英语不好的原因:(。