# 蒙戈布的安全-第 2 部分-授权

> 原文：<https://dev.to/delbussoweb/segurana-no-mongodb---parte-2---autorizao-5h1i>

个人来说，通过延续我们在 mongodb 的一系列安全帖子，今天我们将看到授权部分，您的概念，最后我们将创建两个用户，并授予他们不同的权限。我们走吧-我...。

### 重述

简单总结一下，如果你输了，就可以进入这里的第 1 部分，我们在那里看到认证。在 mongodb 中，身份验证等同于“你是谁？”授权是:“好吧，我知道你是谁。但你能做些什么？”

### 访问控制

与大多数数据库一样，mongodb 使用*基于角色的访问控制* (RBAC)来管理其访问。对于用户，我们可以分配一个或多个*角色*以确定用户可以在数据库中访问和执行哪些功能和操作。一旦启用，任何不属于这些*角色*的东西都不允许用户使用。

### 启用访问控制

我在“[”第 1 部分“](https://dev.to/delbussoweb/segurana-no-mongodb---parte-1-3bik)”中评论说，默认情况下，MongoDB 不启用任何访问控制，这与 SQL Server 不同，例如，在安装过程中会强制我们选择一种身份验证形式。我们可以使用配置参数 [--auth](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-mongod-auth) 或[【security . authorization】](https://docs.mongodb.com/manual/reference/configuration-options/#security.authorization)启用授权。通过启用内部验证，我们还隐式启用授权。

启用访问控制后，用户必须经过身份验证才能执行任何操作。

### 角色

一种*角色*赋予在资源中执行[特定](https://docs.mongodb.com/manual/reference/privilege-actions/#security-user-actions)动作的权限。数据库、集合、集合集或群集被视为资源。

每个权限可在【T1 角色】中明确指定，从另一个【T2 角色】继承而来【T3 角色】或两者皆继承！

### 继承特权

一个【T0 角色】【T1 角色】在其定义中可以包括一个或多个【T2 角色】【T3 角色】，在这种情况下，这个【T4 角色】【T5 角色】继承了所包括的【T6 角色】【T7 角色】的全部权限。也就是说，如果我正在创造一种具有诸如*读取*等特定权限的*角色，并包括一种具有*写入*的*角色，则新的*这听起来可能很复杂，但我们将在后面的实践中看到这一点。*

注:在数据库中创建的【T1 角色】【T2 管理员】【T3 管理员】可以继承任何数据库中【T4 角色】【T5 角色】的权限

### 查看角色权限

要通过命令 [rolesInfo](https://docs.mongodb.com/manual/reference/command/rolesInfo/#dbcmd.rolesInfo) 查看某个*角色的权限，其属性 **showPrivileges** 和**show builtin**设置为 **true***

```
db.runCommand(
    {
      rolesInfo: 1,
      showPrivileges: true
    }
)

db.runCommand(
    {
      rolesInfo: 1,
      showBuiltinRoles: true
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

### 用户和角色

您可以在创建用户的过程中分配*角色*。但也可以通过指派或撤销*角色*来更改现有用户。mongodb 中的所有用户管理方法均可在此链接中找到[。](https://docs.mongodb.com/manual/reference/method/#user-management-methods)

分配了角色的用户将被授予该*角色*的所有权限。一个用户可以有多个*角色*。

**提示**:将用户创建和*角色*集中在一个数据库中很重要(但不是必需的)，我通常使用 **admin** 。这样可以更轻松地维护用户。

### 内置角色 e 用户自定义角色

MongoDB 为我们提供了一系列预定义的【T1 角色】，即【T2 角色】【T3 角色】，具有最常见的数据库管理和访问权限。

但是，如果不想使用这套*角色*，可以创建自己的*角色* [(用户定义角色)](https://docs.mongodb.com/manual/core/security-user-defined-roles/)

## 手放在面团上

好吧，经过这些重要的概念，现在让我们创建用户并授予他们权限。正如我刚才提到的提示，我喜欢用**【admin】**银行集中用户，这就是我要在这里演示的。

### 创建 root 用户

就像 SQL Server 上有一个 SA 用户一样，我们可以在 mongodb 上拥有一个超级用户，我们通常在实施群集时创建该用户，然后禁用该用户，因为我们拥有足够的特定权限来管理该用户。

```
use admin
db.createUser({
    user: "admin",
    pwd: "password",
    roles: [
        { role: "root", db: "admin" }
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

好了！在上面的脚本中，我们创建了一个名为 **admin** 的用户，其密码为 **password** ，并将*角色*root 分配给数据库 **admin**

### 创建读取用户

现在，以我们创建的 root 用户[(见第 1 部分 localhost exception)](https://dev.to/delbussoweb/segurana-no-mongodb---parte-1-3bik)为例，我们将创建一个对名为 **products** 的数据库具有只读权限的用户:

```
use admin
db.auth("admin","password")
db.createUser({
    user: "usrProductsRead",
    pwd: "passProducts",
    roles: [
        { role: "read", db: "products" }
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们需要进行身份验证才能创建新用户，因此，我使用了命令**【db . auth()**，并将用户和密码作为参数传递。然后创建了用户，仍在银行 **admin** 中，但在银行 **products** 中具有读取权限。

今天，目的是在 mongodb 中概述授权的概念，您可以在此链接中找到更多的信息【直接链接到主题】。

希望你们喜欢，下次再见！；）