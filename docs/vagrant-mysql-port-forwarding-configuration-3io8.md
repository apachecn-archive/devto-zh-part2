# 流浪 MySQL 端口转发配置

> 原文：<https://dev.to/makitosan/vagrant-mysql-port-forwarding-configuration-3io8>

*   Ubuntu 16.04
*   MySQL 5.7
*   流浪汉 2.0.2

## 流浪

### 编辑流浪文件

```
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 3306, host: 3306
end 
```

Enter fullscreen mode Exit fullscreen mode

## MySQL(来宾)

### 编辑/etc/MySQL/MySQL . conf . d/mysqld . CNF

```
bind-address            = 0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

### 授予特权

在客户机上，登录到本地 mysql 服务器并执行以下 sql。

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'10.0.2.2' IDENTIFIED BY 'your_password' WITH GRANT OPTION;
FLUSH PRIVILEGES; 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

```
mysql -uroot -p 
```

Enter fullscreen mode Exit fullscreen mode