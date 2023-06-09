# Grails:模仿引用 Joda 时间对象的域对象/标准

> 原文：<https://dev.to/nancyd/grails-mocking-domain-objectscriteria-that-reference-joda-time-objects-a8o>

我可以像其他曾经尝试过的人一样，大声抱怨在编程时正确处理时间的困难。我也可以批评 Java 中的默认日期和时间对象，但我只想说:就用 Joda Time 吧。你也可以看看我不久前写的一篇文章，了解更多细节。我甚至在 Grails 的域对象中使用 Joda Time。它需要少量的配置，但非常值得。详见[插件](https://grails.org/plugin/joda-time?skipRedirect=true)。

事情变得有点复杂的地方是，当标准涉及到`org.joda.time.DateTime`时，我想测试我的 GORM 标准。例如:

```
/** My domain object */
class Membership {
  User user
  DateTime memberSince
}

/** My service */
class MembershipService {
  boolean transactional = true

  /** Return users who have been around for at least 5 years */
  List<User> findOldUsers() {
    Membership.createCriteria().list() {
      user {
        eq('deleted', false)
      }
      lte('memberSince', new DateTime().minusYears(5))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，测试:

```
@Mock([Membership, User])
@TestFor(MembershipService)
class MembershipServiceSpec extends Specification {
  def "findOldUsers does not return anything if all the users have been around less than a year"() {
    given:
      User u1 = new User().save() 
      // or save(false) if there are constraints we want to ignore for the time being)
      new Membership(user: user, memberSince: new DateTime().minusMonths(4)).save();

    expect:
      service.findOldUsers().isEmpty()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

会发生什么？良好的....

```
java.lang.IllegalArgumentException: Property [memberSince] is not a valid property of class [com.mycompany.Membership] 
```

Enter fullscreen mode Exit fullscreen mode

当然，GORM 嘲讽框架并不知道 DateTime，所以并没有将其注册为 Membership 的属性。要解决这个问题，我们需要做的就是，首先告诉 mocking 框架关于 Joda 时间类的信息，其次，我们必须在模仿 Joda 时间类之后模仿我们的域对象。

```
import grails.plugin.jodatime.simpledatastore.SimpleMapJodaTimeMarshaller
@Mock(User)
@TestFor(MembershipService)
class MembershipServiceSpec extends Specification {
  setup() {
    SimpleMapJodaTimeMarshaller.initialize()
    mockDomain(Membership)
  }

  // [...]
} 
```

Enter fullscreen mode Exit fullscreen mode