# 内存泄漏，重定向循环，死锁，XSS，CSRF，XEE，...，通过使用漏洞太多的 web 应用程序来了解漏洞

> 原文：<https://dev.to/ktamura/memory-leak-redirect-loop-deadlock-xss-csrf-xee--understanding-bugs-by-using-too-buggy-web-application-2gmn>

如果你开始开发一个 web 应用程序，你可能会面临许多问题。比如内存泄漏、重定向循环、死锁、连接泄漏等等。万一发生意外故障，你可能需要了解故障，并提前学习/练习如何排除故障。另外，你可能还需要学习如何防范漏洞，例如，XSS、SQL 注入、CSRF、XEE 等等。

所以我创建了一个漏洞太多的 web 应用程序,它可以有意地重现许多问题:

*   困难

    *   内存泄漏(Java 堆空间)
    *   内存泄漏(永久空间/元空间)
    *   内存泄漏(C 堆空间)
    *   死锁(Java)
    *   死锁(SQL)
    *   无尽的等待过程
    *   无限循环
    *   重定向循环
    *   正向循环
    *   JVM 崩溃
    *   网络套接字泄漏
    *   数据库连接泄漏
    *   文件描述符泄漏
    *   螺纹泄漏
    *   莫吉巴克
    *   整数溢出
    *   舍入规则
    *   截断误差
    *   尾部数字丢失
*   脆弱点

    *   XSS(跨站点脚本)
    *   SQL 注入
    *   LDAP 注入
    *   代码注入
    *   操作系统命令注入(OGNL 表达式注入)
    *   邮件头注入
    *   零字节注入
    *   扩展无限制文件上传
    *   无限制文件上传大小
    *   打开重定向
    *   暴力攻击
    *   会话固定攻击
    *   详细的登录错误消息
    *   危险文件包含
    *   目录遍历
    *   意外文件泄露
    *   CSRF(跨站点请求伪造)
    *   XEE (XML 实体扩展)
    *   XXE (XML 外部实体)
    *   点击劫持
*   性能下降

    *   缓慢的正则表达式解析
    *   由于+(加)运算符导致创建字符串的延迟
    *   由于不必要的对象创建造成的延迟
*   Java 错误

    *   断言错误
    *   exceptioniniinitializererror
    *   工厂配置错误
    *   GenericSignatureFormatError
    *   NoClassDefFoundError
    *   OutOfMemoryError (Java 堆空间)
    *   OutOfMemoryError(请求的阵列大小超过虚拟机限制)
    *   OutOfMemoryError(无法创建新的本机线程)
    *   OutOfMemoryError(超出 GC 开销限制)
    *   OutOfMemoryError(永久空格)
    *   直接缓冲存储器
    *   stack over flower error
    *   TransformerFactoryConfigurationError
    *   不满意的链接错误

## 快速启动

1.  进入[最新发布页面](https://github.com/k-tamura/easybuggy/releases/latest/)下载`easybuggy.jar`
2.  运行命令:`$ java -jar easybuggy.jar`
3.  访问 [http://localhost:8080](http://localhost:8080) 。

## 演示

这个演示显示:
启动- >无限循环- > LDAP 注入->UnsatisfiedLinkError->BufferOverflowException->死锁- >内存泄漏- > JVM 崩溃(关闭)

[![demo](img/4492df4efc69f36c6cc9a1d6f7ceb7a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vB6h9W6h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htbk1s3lniudreb26dje.gif)

## 警告

此应用程序可能会导致严重的内存泄漏或增加 CPU 使用率，并使您的计算机不稳定。所以我建议您在虚拟机上运行它。

## 其他版本

EasyBuggy 是用 Java Servlet 3.0 和老技术实现的。您也可以使用您喜欢的其他版本:

*   EasyBuggy Boot :基于 Spring Boot 的 EasyBuggy 克隆版本
*   EasyBuggy Bootlin : EasyBuggy 克隆版，基于 Spring Boot，用科特林语言编写
*   EasyBuggy Django : EasyBuggy 克隆版构建于 Django 2 之上，用 Python 3 编写