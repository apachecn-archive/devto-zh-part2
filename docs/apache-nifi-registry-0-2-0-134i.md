# Apache NiFi 注册表 0.2.0

> 原文：<https://dev.to/bbende/apache-nifi-registry-0-2-0-134i>

Apache NiFi 社区最近完成了 NiFi Registry 的第二个版本(0.2.0)。这篇文章将介绍一些新特性，包括基于 git 的流持久性、更具可配置性的元数据数据库和事件挂钩。

### 背景

在进入新特性之前，让我们回顾一下 NiFi Registry 是如何在幕后工作的。

用户界面是用 Angular 构建的单页面 webapp，通过 NiFi RegistryREST API 与服务器通信。在 REST API 的背后是一个服务层，在这里实现主要的业务逻辑，服务层与元数据数据库和流持久性提供者进行交互。

[![](img/01937b4e39a522b24819ab1f42a0c090.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QtHV7O3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-registry-0_2_0/01-architecture-original.png)

*元数据数据库*存储关于桶和版本化项目的信息，例如标识符、名称、描述和提交注释，以及哪些项目属于哪个桶。最初的版本利用了一个嵌入式 H2 数据库，除了配置目录位置之外，这个数据库对最终用户是隐藏的。

*流持久性提供者*存储每个版本化流的内容，并被视为公共扩展点。这意味着可以通过实现 FlowPersistenceProvider 接口来提供自定义实现。最初的版本提供了流持久性提供者的文件系统实现，它使用本地文件系统来实现持久性。

总体思路是，元数据数据库包含所有类型的版本化项目的信息(最终可能不仅仅是流)，每种类型的项目都有自己的项目内容持久化机制。

### Git flow persistence provider

0.2.0 版本利用 JGit 库提供了一个新的基于 git 的 FlowPersistenceProvider 实现。这意味着版本化流的内容现在可以存储在 git 存储库中。

[![](img/a6d07ef80fd660728a68e11738cdaea9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JRnyXeGi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-registry-0_2_0/02-architecture-git.png)

git 提供者可以通过以下配置在 providers.xml 中进行配置:

```
<flowPersistenceProvider>
    <class>
      org.apache.nifi.registry.provider.flow.git.GitFlowPersistenceProvider
    </class>
    <property name="Flow Storage Directory">./flow_storage</property>
    <property name="Remote To Push"></property>
    <property name="Remote Access User"></property>
    <property name="Remote Access Password"></property>
</flowPersistenceProvider> 
```

*“流存储目录”*属性指定了一个预期已经是 git 储存库的本地目录。这可以通过创建一个新目录并运行“git init”来完成，或者通过克隆一个现有的远程存储库来完成。

*“要推送的远程”*属性指定要自动推送的远程的名称。此属性是可选的，如果未指定，提交将保留在本地存储库中，除非手动执行推送。

注意:要使用 GitHub，请执行以下操作:

*   创建一个新的 GitHub repo 并在本地克隆它
*   将“流存储目录”设置为克隆存储库的目录
*   进入你的帐户的 GitHub 的“开发者设置”
*   为您的 NiFi 注册实例创建一个新的“个人访问令牌”
*   将“远程访问用户”设置为您的 GitHub 用户名
*   将“远程访问密码”设置为访问令牌

### 外部元数据数据库

0.2.0 版本提供了新的配置，允许元数据数据库利用外部数据库。目前 Postgres 是除 H2 之外唯一受支持的数据库，尽管其他数据库可能需要额外的测试。

[![](img/a97096c57663f41d53aeb212a498477c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UtuYtBQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-registry-0_2_0/03-architecture-postgres.png)

0.1.0 版本最初在 nifi-registry.properties 中有两个与 H2 数据库相关的属性:

```
nifi.registry.db.directory=
nifi.registry.db.url.append= 
```

为了提高数据库的可配置性，0.2.0 版本增加了以下属性:

```
nifi.registry.db.url=
nifi.registry.db.driver.class=
nifi.registry.db.driver.directory=
nifi.registry.db.username=
nifi.registry.db.password= 
```

如果您是第一次启动 NiFi Registry，那么属性将默认使用 H2，您不需要做任何事情，除非您想更改配置以使用 Postgres 数据库。

如果要升级现有的 NiFi 注册表，它将确定旧的 nifi.registry.db.directory 属性是否已填充，并确定该目录是否包含现有的 H2 数据库。如果找到现有数据库，并且目标数据库为空，那么数据将自动从现有数据库迁移到新数据库。

注意:这实质上提供了从现有的 H2 数据库到新的 H2 数据库或新的 Postgresdatabase 的一次性迁移。

有关每个属性的具体信息，请参考 [NiFi 注册管理指南](https://nifi.apache.org/docs/nifi-registry-docs/index.html)。

### 事件挂钩

事件挂钩是一个新的扩展点，它允许在应用程序事件发生时触发自定义代码。

为了实现事件挂钩，必须实现以下 Java 接口:

```
public interface EventHookProvider extends Provider {

    void handle(Event event) throws EventHookException;

} 
```

事件对象将包含一个 EventType，以及特定于该事件的字段/值对列表。在撰写本文时，可能的事件类型如下:

```
CREATE_BUCKET
CREATE_FLOW
CREATE_FLOW_VERSION
UPDATE_BUCKET
UPDATE_FLOW
DELETE_BUCKET
DELETE_FLOW 
```

事件类型和字段的列表可以在 [EventType 类](https://github.com/apache/nifi-registry/blob/master/nifi-registry-provider-api/src/main/java/org/apache/nifi/registry/hook/EventType.java)的代码中找到。

0.2.0 版本提供了两个事件挂钩，*loggingeventukprovider*和*ScriptedEventHookProvider*。

#### loggingeventukprovider

*loggingeventukprovider*使用 SLF4J 记录器记录每个事件的字符串表示。记录器可以通过 NiFi Registry 的 logback.xml 进行配置，默认情况下，该文件包含一个 appender，用于写入 logs 目录中名为 nifi-registry-event.log 的日志文件。

要启用这个挂钩，只需取消 providers.xml:
中的配置注释即可

```
<eventHookProvider>
    <class>
      org.apache.nifi.registry.provider.hook.LoggingEventHookProvider
    </class>
</eventHookProvider> 
```

在创建一个 bucket 并对一个流程组启动版本控制之后，事件日志应该显示如下内容:

```
2018-06-19 11:30:46,174 ## CREATE_BUCKET [BUCKET_ID=5d81dc5e-79e1-4387-8022-79e505f5e3a0, USER=anonymous]
2018-06-19 11:32:16,503 ## CREATE_FLOW [BUCKET_ID=5d81dc5e-79e1-4387-8022-79e505f5e3a0, FLOW_ID=a89bf6b7-41f9-4a96-86d4-0aeb3c3c25be, USER=anonymous]
2018-06-19 11:32:16,610 ## CREATE_FLOW_VERSION [BUCKET_ID=5d81dc5e-79e1-4387-8022-79e505f5e3a0, FLOW_ID=a89bf6b7-41f9-4a96-86d4-0aeb3c3c25be, VERSION=1, USER=anonymous, COMMENT=v1] 
```

#### scripteventukprovider

*ScriptedEventHookProvider*允许为每个事件执行一个定制脚本。这可以用来处理许多情况，而不必在 Java 中正式实现事件挂钩。

这个事件挂钩的配置如下:

```
<eventHookProvider>
    <class>
      org.apache.nifi.registry.provider.hook.ScriptEventHookProvider
    </class>
    <property name="Script Path"></property>
    <property name="Working Directory"></property>
</eventHookProvider> 
```

*“脚本路径”*属性是将为每个事件执行的脚本的完整路径。脚本的参数将是事件字段，按照它们为给定事件类型指定的顺序。举个例子，假设我们用以下内容创建了一个名为 logging-hook.sh 的脚本:

```
#!/bin/bash
echo $@ >> /tmp/nifi-registry-event.log 
```

对于每个事件，这会将参数回显到脚本中，并将它们附加到/tmp/nifi-registry-event.log 中

```
CREATE_BUCKET feeb0fbe-5d7e-4363-b58b-142fa80775e1 anonymous
CREATE_FLOW feeb0fbe-5d7e-4363-b58b-142fa80775e1 1a0b614c-3d0f-471a-b6b1-645e6091596d anonymous
CREATE_FLOW_VERSION feeb0fbe-5d7e-4363-b58b-142fa80775e1 1a0b614c-3d0f-471a-b6b1-645e6091596d 1 anonymous v1 
```

### 总结

0.2.0 版本中的新特性为元数据数据库和流持久性提供者提供了额外的选项，允许将数据从运行应用程序的服务器推送至外部位置。此外，事件挂钩提供了一种基于各种事件启动定制工作流的简单方法。