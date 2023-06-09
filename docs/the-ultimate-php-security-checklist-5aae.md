# 终极 PHP 安全清单

> 原文：<https://dev.to/sqreenio/the-ultimate-php-security-checklist-5aae>

*阅读完整清单[此处](https://www.sqreen.io/checklists/php-security-checklist)T3】*

该死，但是安全很难。需要做什么并不总是显而易见的，良好安全性的回报充其量也是模糊的。当它从我们的优先列表中消失时，谁会感到惊讶呢？

这个安全清单旨在为开发人员提供一个 PHP 安全最佳实践列表，他们可以遵循这些实践来帮助提高代码的安全性。

以下是安全检查表中的一些项目:

## 过滤并验证所有数据

不管数据来自哪里，不管是配置文件、服务器环境、GET 和 POST，还是其他任何地方，都不要相信它。过滤并验证它！通过使用一个可用的库，比如 zend-inputfilter，可以做到这一点。

**阅读更多:**

*   [Zend 框架中的验证](https://framework.zend.com/blog/2017-06-13-zend-validator.html)
*   [Symfony 中的验证](https://symfony.com/doc/current/validation.html)
*   [在 Laravel 中验证](https://laravel.com/docs/5.6/validation)

## 使用参数化查询

为了避免 SQL 注入攻击，不要将 SQL 字符串与外部数据连接或插入。请改用参数化查询和预处理语句。这些可以与特定于供应商的库一起使用，或者通过使用 PDO 来使用。

**了解更多:**

*   [在 PDO 准备语句和存储过程](https://secure.php.net/manual/en/pdo.prepared-statements.php)
*   [Mysqli 准备好的报表](https://secure.php.net/manual/de/mysqli.quickstart.prepared-statements.php)
*   [PostgreSQL pg _ query _ params 函数](https://secure.php.net/manual/en/function.pg-query-params.php)

## 设置 open_basedir

`open_basedir`指令限制 PHP 可以从`open_basedir`目录及以下访问文件系统的文件。不能访问该目录之外的任何文件或目录。这样，如果恶意用户试图访问敏感文件，如`/etc/passwd`，访问将被拒绝。

**阅读更多:**

*   [open_basedir 配置指令](https://secure.php.net/manual/en/ini.core.php#ini.open-basedir)
*   [PHP 文件系统安全性](https://secure.php.net/manual/en/security.filesystem.php)
*   [数字海洋的隔离执行环境](https://www.digitalocean.com/community/tutorials/7-security-measures-to-protect-your-servers#isolated-execution-environments)

## 检查您的 SSL / TLS 配置

通过定期扫描，确保服务器的 SSL/TLS 配置是最新的且配置正确，并且没有使用弱密码、TLS 的过期版本、没有弱密钥的有效安全证书等。

**阅读更多:**

*   [SSL 实验室](https://www.ssllabs.com/)
*   Mozilla 的天文台

**查看完整清单[此处](https://www.sqreen.io/checklists/php-security-checklist)T3】**