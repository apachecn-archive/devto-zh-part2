# Verdaccio 和确定性锁文件

> 原文：<https://dev.to/verdaccio/verdaccio-and-deterministic-lock-files-19go>

<figure>[![](../Images/cdca9a8bbd9b3d740922ff8c604ca165.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OSiXwHcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aigz5Q878nju28EAa6RJ_Xg.png) 

<figcaption>一些随机锁定文件的片段</figcaption>

</figure>

**节点包管理器(npm)客户端上的锁文件**并不是一个新话题，yarn 用一个叫做 [**确定性**](https://yarnpkg.com/blog/2017/05/31/determinism/) 的术语打破了节点包管理器的世界，提供了一个在安装后生成的名为 yarn.lock 的新文件来锁定和冻结依赖关系，目的是避免多个安装之间的不一致性。

如果您使用私有注册中心作为 [Verdaccio](https://verdaccio.org/) ，那么在使用私有或本地域作为注册中心 URL 的回购中提交锁文件，然后其他人由于其环境而无法获取锁文件中定义的 tarballs，这可能是一个问题。

这仅仅是所有软件包管理器必须解决的问题，如今不难看到公司使用他们自己的注册表来托管私有软件包，或者使用**Verdaccio**power feature[uplinks](https://verdaccio.org/docs/en/uplinks)来使用一个端点解决来自多个注册表的依赖性。

### 一个锁文件是什么样子的？

根据您使用的包管理器，锁文件看起来不同，以 npm 为例，看起来像这样

```
"[@babel/code-frame](http://twitter.com/babel/code-frame)@7.0.0-beta.44":
 version "7.0.0-beta.44"
 resolved "[http://localhost:4873/@babel%2fcode-frame/-/code-frame-7.0.0-beta.44.tgz#2a02643368de80916162be70865c97774f3adbd9](http://localhost:4873/@babel%2fcode-frame/-/code-frame-7.0.0-beta.44.tgz#2a02643368de80916162be70865c97774f3adbd9)"
 dependencies:
 "[@babel/highlight](http://twitter.com/babel/highlight)" "7.0.0-beta.44" 
```

Enter fullscreen mode Exit fullscreen mode

上面的片段只是这个巨大文件的一小部分，当冲突出现时，没有人敢处理它。但是，我只想让你关注一个名为 **resolved** 的字段。

#### 以 Verdaccio 为本地主机的简单例子

让我们假设您使用 **Verdaccio** 和 **yarn** 用于本地目的，并且您的注册表配置指向。

```
yarn config set registry http://localhost:4873/ 
```

Enter fullscreen mode Exit fullscreen mode

在运行安装 yarn install 之后，会生成一个锁文件，并且每个依赖项都有一个名为 resolved 的字段，该字段准确地指向在将来的安装中应该下载 tarball 的 URI。这意味着无论发生什么，包管理器都将依赖这样的 URI。

在 pnpm 的情况下，锁文件看起来有点不同，我们将在本文后面看到详细的内容。

```
// yarn.lock

math-random@^1.0.1:
 version "1.0.1"
 resolved "[http://localhost:4873/math-random/-/math-random-1.0.1.tgz#8b3aac588b8a66e4975e3cdea67f7bb329601fac](http://localhost:4873/math-random/-/math-random-1.0.1.tgz#8b3aac588b8a66e4975e3cdea67f7bb329601fac)" 
```

Enter fullscreen mode Exit fullscreen mode

让我们想象一下，你可能想改变你的域，你的注册表托管和解决领域仍然指向以前的位置，你的包管理器将无法解决项目的依赖关系了。

**通常的解决方案是删除整个锁文件并生成一个新的**，但是，这对于大型团队来说并不实用，因为这会导致分支之间的冲突难以解决。

那么，*我如何使用私有注册中心来避免* *解决的字段问题呢？*。所有的客户都以不同的方式处理这个问题，让我们看看他们是如何做的。

### 解析字段是如何被…使用的？

[![](../Images/8a91beb89f2402b9c0758c0cdb5bb0bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EEuQCBn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkafHawK1RCt-LDsdGz6iUA.png)

npm 使用 JSON 作为锁文件的格式。好消息是，由于 npm@5.0.0 [忽略了 package-lock.json 文件中已解析的字段](http://blog.npmjs.org/post/161081169345/v500)，并且基本上回退到。如果存在，则使用 CLI 的 npmrc 或 via - registry 参数，否则，它将使用 resolved 字段中定义的。

液体错误:内部

现在，您可以使用 npm cli 和锁定文件安全地与 Verdaccio 独立的网址 tarball 服务。但是，我建议你分享一个本地的。npmrc 文件，注册表默认设置在本地，或者通知您的团队。

[![](../Images/011d79ad363448ff3a0749f0499e9e52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QVHtoLsZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0pWUcgRyhax5KVJKsnbgkA.png)

如果你用的是纱线，情况就有点不同了。直到版本 1.9.4，它试图解决什么锁文件定义为第一选项。

有一些关于公共关系、RFC 或门票的参考资料，它们讨论了如何正确解决这个问题，如果您愿意深入这个主题，请允许我分享您可能关注的最有趣的线索:

*   用哈希替换解析的字段[https://github . com/yarn pkg/rfcs/pull/64 # issue comment-414649518](https://github.com/yarnpkg/rfcs/pull/64#issuecomment-414649518)
*   yarn.lock 不应包含基本域名注册表[https://github.com/yarnpkg/yarn/issues/3330](https://github.com/yarnpkg/yarn/issues/3330)
*   从锁定文件中删除主机名[https://github.com/yarnpkg/yarn/issues/5892](https://github.com/yarnpkg/yarn/issues/5892)

> TDLR；Yarn 2.0 [已经计划在下一个主要版本](https://github.com/yarnpkg/yarn/projects/4#card-10080906)中解决这个问题，直到今天[还在讨论采取什么方法](https://github.com/yarnpkg/rfcs/pull/64#issuecomment-414163196)。

<figure>[![](../Images/973a72dbfa6c3fa9f5be98de8180d8ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GlpWevXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1012/1%2AY3jjekoNQiujCccP3bNvTg.png)

<figcaption></figcaption>

</figure>

[**pnpm**](https://pnpm.js.org/) 遵循与生成锁文件的其他包管理器相同的方法，但是在这种情况下，该文件被称为基于 **yaml 格式的 shrinkwrap.yaml。**

```
dependencies:
 jquery: 3.3.1
 parcel: 1.9.7
packages:
 /@mrmlnc/readdir-enhanced/2.2.1:
 dependencies:
 call-me-maybe: 1.0.1
 glob-to-regexp: 0.3.0
 dev: false
 engines:
 node: '\>=4'
 resolution:
 integrity: sha512-bPHp6Ji8b41szTOcaP63VlnbbO5Ny6dwAATtY6JTjh5N2OLrb5Qk/Th5cRkRQhkWCt+EJsYrNB0MiL+Gpn6e3g==
 tarball: /@mrmlnc%2freaddir-enhanced/-/readdir-enhanced-2.2.1.tgz

....

registry: '[http://localhost:4873/'](http://localhost:4873/')
shrinkwrapMinorVersion: 9
shrinkwrapVersion: 3
specifiers:
 jquery: ^3.3.1
 parcel: ^1.9.7 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子只是这个长文件的一小部分，您可能会注意到在锁文件的底部添加了一个名为 [registry](https://github.com/pnpm/spec/blob/master/shrinkwrap/3.8.md#registry) 的字段，引入[是为了减小锁文件](https://github.com/pnpm/pnpm/issues/1072)的文件大小，在某些情况下，pnpm 决定设置[，该域是 tarball 字段](https://github.com/josephschmitt/pnpm-406-npmE)的一部分。

**pnpm** 会像 yarn **和**一样，尝试使用锁文件中定义的注册表来获取依赖项。然而，作为一种变通办法，如果域发生变化，您必须手动更新注册表字段，这并不难做到，但总比什么都没有好。

pnpm 已经开了一个票来推动这个问题，我会让下面的链接。

[从“shrinkwrap.yaml”问题#1353 pnpm/pnpm 中删除“注册表”字段](https://github.com/pnpm/pnpm/issues/1353)

### 作用域注册表解决方法

路由私有包的一种常见方式是通过不同的注册中心来路由作用域依赖项。这适用于 npm 和 pnpm

```
registry=[https://registry.npmjs.org](https://registry.npmjs.org/)
@mycompany:registry=http://verdaccio-domain:4873/ 
```

Enter fullscreen mode Exit fullscreen mode

> 在撰写本文时，它确实存在任何支持。

在我看来，这只是一种变通方法，要看你处理的数量或者作用域来决定是否值得。此外，包管理器将绕过那些与范围不匹配的包，并且不会被您的私有注册表解析。

### 结论

**包管理器**正致力于用向后兼容性和良好的性能来解决这个问题。

目前，如果您也有这种担心，最好的解决方案是**使用 npm，直到其他客户端按照上面针对每个客户端**的建议或**决定做什么。**

* * *