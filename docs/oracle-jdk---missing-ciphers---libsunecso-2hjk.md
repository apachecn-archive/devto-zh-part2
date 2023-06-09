# 甲骨文 JDK -遗失的密码- libsunec.so

> 原文：<https://dev.to/risdenk/oracle-jdk---missing-ciphers---libsunecso-2hjk>

向@quirogadf 大声呼喊，他深入研究并找到了下面的大部分信息。

### 概述

如果安装了[Java Cryptography Extension](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)，Oracle JDK 支持基于 JDK 版本的特定密码和协议集。在 Java 8 和 9 中，JCE 需求有了新的变化，默认安装并启用了。在与包括 [Apache Hadoop](https://hadoop.apache.org/) 和 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 在内的许多 Java 项目合作时，我们希望确保 TLS/SSL 使用强密码。在尝试启用强密码时，我们发现我们的供应商安装的 JDK 似乎不支持这些密码。

### 评估支持的 JDK 密码

下面的`jrunscript`命令将输出 TLS/SSL 支持哪些密码。

```
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLServerSocketFactory.getDefault().getSupportedCipherSuites()))" 
```

Enter fullscreen mode Exit fullscreen mode

### 比较支持的和预期的密码

对于我们的一个节点，我们注意到上面命令的输出如下:

```
[TLS_RSA_WITH_AES_256_CBC_SHA256, TLS_DHE_RSA_WITH_AES_256_CBC_SHA256, TLS_DHE_DSS_WITH_AES_256_CBC_SHA256, TLS_RSA_WITH_AES_256_CBC_SHA,.... 
```

Enter fullscreen mode Exit fullscreen mode

这缺少了我们感兴趣的更强的密码，比如[椭圆曲线加密](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)密码。我们要找的特定密码是:

*   `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
*   `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
*   `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
*   `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

### 寻找丢失的密码

由于我们没有运行最新的 JDK 8 版本，我们需要检查我们已经正确安装了 JCE。下面的脚本将根据支持的密钥长度检查是否安装了 JCE。

```
jrunscript -e 'exit (javax.crypto.Cipher.getMaxAllowedKeyLength("RC5") >= 256);' || if [$? -eq 1]; then echo "JCE Installed"; else echo "JCE Not Installed or Error"; fi 
```

Enter fullscreen mode Exit fullscreen mode

JCE 已正确安装，但我们仍然无法列出所有的 ECC 密码。我们安装了一个单独的 Oracle JDK，看看这是否是现有 JDK 安装的问题。我们发现，随着新的 JDK 安装，所有的密码都可用。这表明我们的供应商安装的 JDK 不同于标准的 JDK 安装。

我们比较了供应商安装的 JDK 和标准 Oracle JDK 中的文件。经过一番努力，我们发现`libsunec.so`不见了。没有`libsunec.so`，某些密码不可用，包括 ECC 密码。我们发现，如果本地库不可用，那么这些密码就不可用。

> [……]Java 类被打包到 JRE 扩展目录中的签名 sunec.jar 中，C++和 C 函数被打包到 JRE 本地库目录中的 libsunec.so 或 sunec.jar 中。如果本地库不存在，则该提供程序注册为支持较少的 ECC 算法(省略了 KeyPairGenerator、Signature 和 KeyAgreement)。

### 添加`libsunec.so`并检查密码

我们将`libsunec.so`从标准的 Oracle JDK 复制到供应商安装的 JDK 位置，并重新检查可用的密码。

```
jrunscript -e "print(java.util.Arrays.toString(javax.net.ssl.SSLServerSocketFactory.getDefault().getSupportedCipherSuites()))"
[TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384, TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384, TLS_RSA_WITH_AES_256_CBC_SHA256, TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384, TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384, TLS_DHE_RSA_WITH_AES_256_CBC_SHA256, TLS_DHE_DSS_WITH_AES_256_CBC_SHA256, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA, TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA, TLS_RSA_WITH_AES_256_CBC_SHA,... 
```

Enter fullscreen mode Exit fullscreen mode

正如上面的输出所示，在复制了`libsunec.so`之后，我们能够确认强密码是可用的。我们联系了我们的供应商，以确保 JDK 与`libsunec.so`包装正确。我们已经将`libsunec.so`复制到了供应商 JDK 的位置，目前正在根据需要使用更强的密码。