# gentoo eix-更新失败

> 原文：<https://dev.to/foursixnine/gentoo-eix-update-failure-en7>

## 总结

如果您的 Gentoo 系统出现以下错误:

```
Can't open the database file '/var/cache/eix/portage.eix' for writing (mode = 'wb') 
```

不要浪费时间，只要/var/cache/eix 目录不存在和/或可由 eix/portage 使用
写入即可

```
mkdir -p /var/cache/eix
chmod +w /var/cache/eix* 
```

基本情况是，当作为根用户运行时，eix 将放弃 portage 用户的权限。