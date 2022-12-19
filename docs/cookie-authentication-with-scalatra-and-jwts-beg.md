# 使用 Scalatra 和 JWTs 进行 Cookie 认证

> 原文：<https://dev.to/jennifer/cookie-authentication-with-scalatra-and-jwts-beg>

*原贴于[jmkoni.com](https://www.jenniferkonikowski.com/blog/2018/5/2/cookie-authentication-using-scalatra-and-jwts)T3】*

作为我与阿卡迪亚合作的一部分，我构建了一个 Rails 应用程序，它添加了一个包含 T2 JWT 的 cookie。太好了！这相当简单。然后我不得不转向我们的 Scala 应用程序，让它接受 JWT 作为标识。现在，我们保持它非常简单，我们只关心它是否有效。这篇文章将涵盖我认为最简单的方法，从开始到结束。或者你可以跳过这些，直接去看《T4》的全部要点。

我们想从 JWT 解析开始。在我们添加代码来真正做到这一点之前，让我们添加一些测试！我决定使用 [JWT Scala 库](http://pauldijou.fr/jwt-scala/)，特别是 [jwt-core](http://pauldijou.fr/jwt-scala/samples/jwt-core/) 。在我看来，这是最容易理解的文档，所以我确实可以使用 RTFM 来完成我的工作。因为我不需要在实际的应用程序中添加编码(令牌将在另一个应用程序中编码)，所以我添加了一个快捷行来在测试中编码令牌。