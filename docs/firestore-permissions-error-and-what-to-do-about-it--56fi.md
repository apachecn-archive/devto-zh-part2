# Firestore 权限错误及其应对措施

> 原文：<https://dev.to/mrmurphy/firestore-permissions-error-and-what-to-do-about-it--56fi>

在 Firebase 上编写“云功能”时，我的电脑无法写入 firestore，而我同事的电脑可以。无论我注销和登录多少次，都得到这个错误:

```
error: { Error: Missing or insufficient permissions.
    at /.../functions/node_modules/grpc/src/client.js:554:15 code: 7, metadata: Metadata { _internal_repr: {} } } 
```

Enter fullscreen mode Exit fullscreen mode

问题原来与再次使用`firebase logout`和`firebase login`有关。对 GitHub 问题的回复提供了一个解决方法:

[https://github . com/firebase/firebase-tools/issues/344 # issue comment-303848830](https://github.com/firebase/firebase-tools/issues/344#issuecomment-303848830)

具体来说:

> *   Install and initialize g cloud:
> *   [https://cloud.google.com/sdk/downloads#interactive](https://cloud.google.com/sdk/downloads#interactive)
> *   Run gcloud auth application-login to any directory by default.
> *   运行 firebase 仅供服务功能