# 如何加密你的 Maven 密码

> 原文：<https://dev.to/scottshipp/how-to-encrypt-your-maven-password-408d>

尽管 Maven 文档中有一整页都是关于他们的密码加密特性的，但它并没有告诉你如何加密 Maven 密码。

**我在说什么？**
如果您的组织中有 Maven repos 的认证，您通常会将用户名和密码存储在默认位于`~/.m2/settings.xml`的 Maven 设置文件中。

例如，我的 settings.xml 中可能有这样的内容:

```
<servers>
    <server>
      <id>myorg-internal-repo</id>
      <username>scott.shipp</username>
      <password>notMyRealPasswordForAnything</password>
    </server>
</servers> 
```

Enter fullscreen mode Exit fullscreen mode

显然，像这样在明文文件中存储密码是愚蠢的。

[![carpenter_1_md](img/0fe504f2a454f6a90d5dc9dd0ea2f0c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OEfeoIpQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://code.scottshipp.com/wp-content/uploads/2017/09/carpenter_1_md.gif)

**如何加密和替换密码**
要解决这个问题，请遵循以下步骤:

*创建主密码*

1.  首先，您必须创建一个主密码，用于加密所有其他 Maven 密码。从打开一个终端开始。
2.  类型:

    ```
    $ mvn --encrypt-master-password
    ```

3.  系统将提示您输入主密码。在此输入密码。
4.  Maven 会吐出一大串长串这样的:

    ```
    {w5+NYEttGTAHV3FanFoel4N5uUmbcvtzRoWZHI5N97jtssbo0O/93W/XLlm0caeM}

    ```

在您执行下一步时，请保持此终端窗口打开。

*存储主密码*

1.  在~/.m2 目录中创建一个名为 settings-security.xml 的文件。
2.  将以下块复制/粘贴到新文件中:

    ```
    <settingsSecurity>
      <master></master>
    </settingsSecurity>

    ```

3.  复制/粘贴 Maven 在前面的步骤中吐出的大而长的加密字符串到主标签**<>**之间。你会以这样的话结束:

    ```
    <settingsSecurity>
      <master>{w5+NYEttGTAHV3FanFoel4N5uUmbcvtzRoWZHI5N97jtssbo0O/93W/XLlm0caeM}</master>
    </settingsSecurity>

    ```

4.  显然，保存 security-settings.xml 文件！

*加密您的密码*

1.  在给定的示例中，settings.xml 服务器条目的密码为“notMyRealPasswordForAnything”。这就是我们想要加密的内容。所以，如果你还没有终端，就打开它。
2.  类型:

    ```
    $ mvn --encrypt-password

    ```

3.  输入您想要加密的密码(在我们的假示例场景中，它是‘notMyRealPasswordForAnything’)。
4.  Maven 将吐出一个加密字符串，该字符串看起来类似于它吐出的主密码的加密字符串。
5.  复制它吐出的新字符串。
6.  打开 settings.xml 文件。
7.  删除当前 **<密码>** 标签之间的密码。
8.  粘贴新的加密版本。
9.  保存文件。
10.  验证 Maven 仍然可以访问有问题的回购。

你们都完了！聪明！