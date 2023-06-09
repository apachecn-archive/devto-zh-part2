# 在 Java 中按字段排序对象列表

> 原文：<https://dev.to/codebyamir/sort-a-list-of-objects-by-field-in-java-3coj>

*最初发表在我的博客上[www.codebyamir.com](http://www.codebyamir.com)T3】*

# 概述

Java 提供了许多对列表进行排序的方法。在本文中，我们将演示如何按字段对对象列表进行排序。

* * *

# 对象列表

我们将在例子中使用下面的用户类:

```
package com.example.demo;

import java.util.Date;

public class User {
  private long id;
  private String email;
  private Date createdOn;

  // other getters and setters omitted

  public Date getCreatedOn() {
    return createdOn;
  }

  public void setCreatedOn(Date createdOn) {
    this.createdOn = createdOn;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们有一个返回所有用户列表的服务方法:

```
List<User> users = userService.getAllUsers(); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 按字段排序对象列表

因此，我们有一个用户列表，并希望按照创建日期对他们进行排序。

让我们介绍几种不同的方法，并探讨每种方法的优点和局限性。

## 选项 1: Collections.sort()具有可比性

我们可以使用`Comparable`接口来定义一个类的默认排序标准。

这需要实现如下所示的`compareTo()`方法:

```
package com.example.demo;

import java.util.Date;

public class User implements Comparable<User> {
  private long id;
  private String email;
  private Date createdOn;

  // other getters and setters omitted

  public Date getCreatedOn() {
    return createdOn;
  }

  public void setCreatedOn(Date createdOn) {
    this.createdOn = createdOn;
  }

  @Override
  public int compareTo(User u) {
    if (getCreatedOn() == null || u.getCreatedOn() == null) {
      return 0;
    }
    return getCreatedOn().compareTo(u.getCreatedOn());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以调用`Collections.sort()`来执行排序。

**按创建日期升序排序**

```
Collections.sort(users); 
```

Enter fullscreen mode Exit fullscreen mode

**按创建日期降序排序**

```
Collections.sort(users);
Collections.reverse(users); 
```

Enter fullscreen mode Exit fullscreen mode

这个选项需要修改我们的类并定义一个默认的(或自然的)排序顺序。降序排序需要两次单独的方法调用。

此选项不允许在我们选择的字段之外的其他字段上排序。

## 选项 2:带有比较器的 Collections.sort()

我们可以向`Collections.sort()`传递一个比较器，而不是修改我们的类。下面的例子使用匿名类创建了一个比较器。

**按创建日期升序排序**

```
Collections.sort(users, new Comparator<User>() {
  @Override
  public int compare(User u1, User u2) {
    return u1.getCreatedOn().compareTo(u2.getCreatedOn());
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

**按创建日期降序排序**

我们交换 u1 和 u2 的顺序来颠倒排序顺序。

```
Collections.sort(users, new Comparator<User>() {
  @Override
  public int compare(User u1, User u2) {
    return u2.getCreatedOn().compareTo(u1.getCreatedOn());
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

此选项允许类外的代码指定排序条件和顺序。

然而，它需要一个难看的匿名类，可读性不强。

## 选项三:列表接口 sort()【Java 8】

Java 8 在 List 接口中引入了一个可以使用比较器的排序方法。

`Comparator.comparing()`方法接受作为比较基础的方法引用。因此，我们通过`User::getCreatedOn`按照 createdOn 字段进行排序。

**按创建日期升序排序**

```
users.sort(Comparator.comparing(User::getCreatedOn)); 
```

Enter fullscreen mode Exit fullscreen mode

**按创建日期降序排序**

```
users.sort(Comparator.comparing(User::getCreatedOn).reversed()); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，代码变得更加简单和易读。

## 选项 4:流接口排序()【Java 8】

假设我们不想修改原始列表，而是返回一个新的排序列表。

在这种情况下，我们可以使用 Java 8 中引入的`Stream`接口中的`sorted()`方法。

**返回按创建日期升序排序的新列表**

```
List<User> sortedUsers = users.stream()
  .sorted(Comparator.comparing(User::getCreatedOn))
  .collect(Collectors.toList()); 
```

Enter fullscreen mode Exit fullscreen mode

**返回按创建日期降序排列的新列表**

```
List<User> sortedUsers = users.stream()
  .sorted(Comparator.comparing(User::getCreatedOn).reversed())
  .collect(Collectors.toList()); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 结论

虽然讨论的每个选项都执行排序，但推荐使用最后两个选项，因为它们更易于编码、调试和阅读。