# secitem 无限制删除

> 原文：<https://dev.to/zeitschlag/secitemdelete-without-limits-3ppo>

我和一个朋友一起用 good ol' Objective-C 编写了一个 iOS 的小应用程序。这是一个(另一个)小的时间跟踪应用程序，有一个可选的后端。

后端的凭证将存储在钥匙串中。当然，用户也应该能够再次删除这些凭证。为了从钥匙链中删除东西，我使用了`SecItemDelete` ( [文档](https://developer.apple.com/documentation/security/1395547-secitemdelete?language=objc))与相同的，下面的查询- `NSDictionary`至于`SecItemCopyMatching` ( [文档](https://developer.apple.com/documentation/security/1398306-secitemcopymatching?language=objc) ):

```
@{
    (__bridge NSString *)kSecClass: (__bridge NSString *)kSecClassGenericPassword,
    (__bridge NSString *)kSecMatchLimit: (__bridge NSString *)kSecMatchLimitOne,
    (__bridge NSString *)kSecAttrService: kKOKeychainKeyService
} 
```

但是没用:从钥匙扣里取回东西的时候，一切都很好。但是当使用查询 for 删除时，Xcode 一直告诉我，`OSStatus`是`-50`:

> 传递给函数的一个或多个参数无效。—[osstatus.com](https://www.osstatus.com/search/results?platform=all&framework=Security&search=-50#)

我花了一些时间才弄明白，但是`SecItemDelete`和`kSecMatchLimitOne`不兼容。一方面，这确实有意义，因为你不知道哪个条目会被删除。另一方面，文档告诉我:

> 有关如何构造搜索词典的信息，请参见 SecItemCopyMatching 函数。— [文档](https://developer.apple.com/documentation/security/1395547-secitemdelete?language=objc#discussion)

它没有说的是，这个`kSecMatchLimit`-行为是不同的:为了限制删除，你必须指定一个额外的键。

从字典中删除`kSecMatchLimit`后，删除钥匙串项目成功了。

再次感谢阅读！