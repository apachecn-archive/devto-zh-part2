# Java - JVM 安全信息收集

> 原文：<https://dev.to/risdenk/java---jvm-security-information-gathering-1pl1>

### 概述

在过去大约 5 年的时间里，我使用了相当多不同的 Hadoop 集群。在这段时间里，Java 发生了很大的变化，每个版本都支持不同的安全特性。此外，每个部署对所需的安全配置有不同的要求。下面的例子展示了如何使用 [`jrunscript`](https://docs.oracle.com/javase/9/tools/jrunscript.htm#JSWOR750) 从命令行快速获取 JVM 安全配置。这不需要将 JAR 文件移动到不同的机器上，并且可以很容易地在许多环境中运行。这些命令应该至少可以在 Java 7 到 Java 9 中运行。理解以下命令的输出需要阅读 Java 安全文档，其中一些是这里的、这里的和这里的。

### 安全王国

[`SecureRandom`](https://docs.oracle.com/javase/9/docs/api/java/security/SecureRandom.html) 是一个提供了“密码强随机数生成器(RNG)”的类。根据 JDK 配置的不同，安全性和性能级别也不同。以下命令打印出标准`SecureRandom()`和`SecureRandom.getInstanceStrong()`的可用提供者和算法。示例还展示了如何用 Java 属性调整`SecureRandom`的配置。

```
jrunscript -e "sr = new java.security.SecureRandom(); print(sr.getProvider() + ' - ' + sr.getAlgorithm())"
jrunscript -Djava.security.egd=file:/dev/./urandom -e "sr = new java.security.SecureRandom(); print(sr.getProvider() + ' - ' + sr.getAlgorithm())"
jrunscript -e "sr = java.security.SecureRandom.getInstanceStrong(); print(sr.getProvider() + ' - ' + sr.getAlgorithm())"
jrunscript -Djava.security.egd=file:/dev/./urandom -e "sr = java.security.SecureRandom.getInstanceStrong(); print(sr.getProvider() + ' - ' + sr.getAlgorithm())" 
```

Enter fullscreen mode Exit fullscreen mode

### Security.getAlgorithms()

[`Security.getAlgorithms()`](https://docs.oracle.com/javase/9/docs/api/java/security/Security.html#getAlgorithms-java.lang.String-) 返回指定服务的可用算法。这有助于确定您正在运行的 JVM 是否支持所需的算法。

```
jrunscript -e "print(java.security.Security.getAlgorithms('SecureRandom'))"
jrunscript -e "print(java.security.Security.getAlgorithms('KeyStore'))"
jrunscript -e "print(java.security.Security.getAlgorithms('Cipher'))" 
```

Enter fullscreen mode Exit fullscreen mode

### Security.getProviders()

[`Security.getProviders()`](https://docs.oracle.com/javase/9/docs/api/java/security/Security.html#getProviders--) 提供了一种列出所有已安装的提供程序的方式，以便根据偏好使用它们。如果您有一个自定义提供程序或正在检查是否有标准提供程序可用，此命令会有所帮助。

```
jrunscript -e "print(java.util.Arrays.toString(java.security.Security.getProviders()))" 
```

Enter fullscreen mode Exit fullscreen mode

### TLS/SSL 密码套件

随着 TLS/SSL 变得越来越重要，为 JVM 配置合适的密码套件变得至关重要。以下示例显示了支持的密码套件以及默认启用的密码套件。这有助于调试服务器和客户端期望启用不同密码套件的问题。

```
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLServerSocketFactory.getDefault().getDefaultCipherSuites()))"
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLServerSocketFactory.getDefault().getSupportedCipherSuites()))"
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLContext.getDefault().getDefaultSSLParameters().getCipherSuites()))"
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLContext.getDefault().getSupportedSSLParameters().getCipherSuites()))" 
```

Enter fullscreen mode Exit fullscreen mode