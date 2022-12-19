# Gradle 签名 Eclipse 插件

> 原文：<https://dev.to/kengotoda/sign-eclipse-plugin-by-gradle-4md8>

当我们发布 Eclipse 插件时，最好签署您的。jar 文件通过`jarsigner`与 JDK 捆绑在一起。通过这个过程，用户可以安装你的插件，而不会像这样警告。在 Eclipse wiki 中，我们有 [Jar 签名页面](https://wiki.eclipse.org/JAR_Signing)，但是它没有解释详细的签名方法。让我们来看看格雷尔是如何签名的？jar 文件。

要通过`jarsigner`签名，您需要存储 keychain 的 [Java Keystore](https://en.wikipedia.org/wiki/Keystore) 文件。我们可以使用`keytool`来生成自签名，但是这里我们将使用[来加密](https://letsencrypt.org/)来生成非自签名。

首先，遵循“让我们加密”的交互，然后您可以生成`privkey.pem`和`fullchain.pem`。其次，使用`openssl`命令生成生成 Java 密钥库所必需的 [.p12 文件](https://en.wikipedia.org/wiki/PKCS_12)。下面是例子:

```
$ openssl pkcs12 -export \
  -in fullchain.pem -inkey privkey.pem \
  -out your.p12 \
  -name eclipse-plugin 
```

第三，生成。jks 文件由`keytool`命令:

```
$ keytool -importkeystore \
  -srckeystore your.p12 -srcstorepass $SRC_PASS -srcstoretype PKCS12 \
  -destkeystore your.jks -deststorepass $DEST_PASS 
```

我们用这个。jks 文件来签名，所以记住你的`$DEST_PASS`。这个你也要留着。jks 文件机密。如果你使用 Travis CI，[加密文件命令](https://docs.travis-ci.com/user/encrypting-files/)是你的朋友。

好了，现在我们有了所有必要的东西，让我们配置我们的`build.gradle`文件。
你需要的是一个函数来签署两个`plugins` jar 文件和`feature` jar 文件。Groovy 本身不提供签名功能，所以使用 [Ant 的 SignJar 任务](https://ant.apache.org/manual/Tasks/signjar.html)，如下图:

```
// sign all .jar file under specified dir
ext.signJar = { File dir ->
  def keystorepass = project.hasProperty('DEST_PASS') ? keystorepass : '';
  if (keystorepass.isEmpty()) {
    print 'to sign eclipse plugins, set "DEST_PASS" project property'
    return
  }

  dir.traverse(type: FILES, nameFilter: ~/.*\.jar$/) {
    def relativePath = rootDir.toPath().relativize( it.toPath() )
    println "signing ${relativePath}"
    ant.signjar(
        destDir: it.parentFile,
        jar: it,
        alias: 'eclipse-plugin',
        keystore: "path/to/your.jks",
        storepass: keystorepass,
        tsaurl: 'http://timestamp.digicert.com',
        verbose: true
    )
  }
} 
```

干得好！您可以在 jar 任务和`artifacts.xml`文件生成之间简单地调用这个函数(因为这个`artifacts.xml`包含。jar 文件)。

这里有一个介绍 jarsigner 到 SpotBugs build 的 PR，你可以把它作为工作解决方案查看。它使用的是 Gradle 5.0 RC3，但应该也能与 Gradle 4 兼容。