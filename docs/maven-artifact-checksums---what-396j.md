# Maven 神器校验和-什么？

> 原文：<https://dev.to/khmarbaise/maven-artifact-checksums---what-396j>

如果你使用的是 [Apache Maven](https://maven.apache.org) ，你可能会面临这样的[问题](https://stackoverflow.com/search?tab=newest&q=%5bmaven%5d%20invalid%20LOC%20header) :

```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-shade-plugin:2.1:shade (default) on project cores-batch: Error creating shaded jar: invalid LOC header (bad signature) -> [Help 1]
...
.... (remove many lines for brevity).
...
Caused by: java.util.zip.ZipException: invalid LOC header (bad signature)
    at java.util.zip.ZipFile.read(Native Method)
    at java.util.zip.ZipFile.access$1400(ZipFile.java:56)
    at java.util.zip.ZipFile$ZipFileInputStream.read(ZipFile.java:679)
    at java.util.zip.ZipFile$ZipFileInflaterInputStream.fill(ZipFile.java:415)
    at java.util.zip.InflaterInputStream.read(InflaterInputStream.java:158)
    at java.io.FilterInputStream.read(FilterInputStream.java:107)
... 
```

怎么会这样呢？大多数情况下，这些都是下载/网络问题造成的。在极少数情况下，也可能是硬件错误(但通常我对此有所怀疑)。这意味着下载的工件没有被正确下载，或者是从不再存在的存储库中下载的。或者任何你能想象到的奇怪的东西。如果有包含 html 片段的工件，这表明您正试图从不再存在的存储库中下载工件。这意味着您必须检查您所使用的存储库的配置，这显然是错误的。

所以现在的问题是:你能做些什么来防止将来发生这种情况？

如果你看一下与此相关的 Stackoverflow 。或多或少所有的答案都会告诉你删除你的本地缓存`$HOME/.m2/repositoy`并重建。
不幸的是，这只是试图解决一个症状，而不是真正的原因。因此，工作的第一步是删除区域缓存。

现在是非常重要的部分:

您必须配置 Maven 来检查下载的工件的校验和，如果它们不正确，您的构建就会失败。这被称为*校验和策略*，我强烈推荐使用。

这意味着您必须更改`settings.xml`中的配置。这意味着你必须改变你的`settings.xml`中的[校验和策略](https://maven.apache.org/settings.html#Repositories)。一个临时的解决方案是用:`--strict-checksums`来调用 maven，它确实能做到这一点，但只是对 Maven 的适当调用，而不是所有的调用。所以最好将它配置到您的`settings.xml`中，看起来像这样:

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <profiles>
    <profile>
      ...
      <repositories>
        <repository>
          <id>codehausSnapshots</id>
          <name>Codehaus Snapshots</name>
          <releases>
            <enabled>false</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
          </releases>
          <snapshots>
            <enabled>true</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
          </snapshots>
          <url>...</url>
        </repository>
      </repositories>
      <pluginRepositories>
        ...
      </pluginRepositories>
      ...
    </profile>
  </profiles>
  ...
</settings> 
```

此外，您必须为您的`settings.xml`中的所有存储库进行配置。如果您在本地或在公司环境中使用存储库管理器。您还必须检查您的存储库管理器是否被正确配置为检查校验和。你当然不应该忘记检查你是否通过`https://`而不是`http://`从你所有的远程仓库下载。