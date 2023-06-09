# Angular 中基于策略的客户端加密

> 原文：<https://dev.to/mkerndt/policy-based-client-side-encryption-in-angular-4k6l>

我和我的团队一直在研究如何将客户端加密作为数据隐私架构的一部分。它最初发表在《角度的深度》上。看一看[这里](https://blog.angularindepth.com/policy-based-client-side-encryption-in-angular-b47068b26d50)！尽情享受吧！

数据呈指数级增长— [世界上 90%的数据都是在过去两年内创建的](https://unctad.org/en/Pages/DTL/STI_and_ICTs/ICT4D-Legislation/eCom-Data-Protection-Laws.aspx)。监管环境正在发生变化— [70%的国家已经采用了全面的数据隐私法](https://unctad.org/en/Pages/DTL/STI_and_ICTs/ICT4D-Legislation/eCom-Data-Protection-Laws.aspx)。因此，软件开发人员正在重新思考他们的数据隐私架构(或缺乏数据隐私架构)。

在本文中，您将学习如何:

*   将客户端加密作为数据隐私架构的一部分来实施。
*   将如何对数据进行分类的决策*(例如，个人健康信息)*与谁可以访问该数据的决策*(例如，西雅图圣恩医院，格雷医生)*分开。
*   定义可审计和测试的一致的数据隐私政策。
*   了解对称加密、非对称加密和转换加密。

我们将使用的一些角度类别包括`HttpInterceptor`和`ClassDecorator`。

## 加密作为一个跨领域的问题

横切关注点是在程序的许多部分重复的代码，但是这些代码与程序的主要功能无关。典型的例子是日志和加密。

[![Cross-cutting Concern](img/95d46f1fa5a945797c49a352668a2599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xtJxf22b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iidolqblzf7tei7j0pa5.png)

隔离横切关注点是*不要重复自己*(干)的一个例子。它加速了开发，保持了非功能需求(nfr)在大型团队中的一致性，并简化了审计和测试。

在 Angular 中，我们使用一个`HttpInterceptor`来实现客户端加密，作为一个横切关注点。

[Angular](https://blog.angularindepth.com/insiders-guide-into-interceptors-and-httpclient-mechanics-in-angular-103fbdb397bf)中拦截器和 HttpClient 机制的内部指南。

`HttpInterceptor`是 Angular 4.3 中引入的中间件概念，是`@angular/common/http`的一部分。框架类`HttpClient`可以在模块初始化时配置一个或多个`HttpInterceptors`。`HttpClient`根据注册的顺序将`HttpInterceptors`链在一起。每个拦截器都有机会在离开时修改`HttpRequest`,在返回时修改`HttpResponse`。该链通过调用框架类`HttpService`结束，该框架类发送`HttpRequest`并监听`HttpResponse`。

[![HTTPInterceptor](img/6ce5b3a51853d0516c3ba1b80329fc4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BKnCZ-Pu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9opapaapr40b0k16vix6.jpeg)

`HttpInterceptor`提供了一个干净的、支持良好的机制来将定制逻辑插入 HTTP 管道。

## 一个日志记录 HttpInterceptor

让我们用一个`HttpInterceptor`来说明这个概念，它只是将一条消息记录到控制台，并链接到下一个处理程序。

```
@Injectable()
export class LoggingHttpInterceptor implements HttpInterceptor {
 intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
   console.log(`${req.url} has been intercepted!`);
   return next.handle(req);
 };
} 
```

Enter fullscreen mode Exit fullscreen mode

`LoggingInterceptor`在`AppModule`与`{ provide: HTTP_INTERCEPTORS, useClass: LoggingHttpInterceptor, multi: true}`注册为提供商，如下所示。

```
@NgModule({
  imports: [
    BrowserModule,
    ...
  ],
  declarations: [ AppComponent ],
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: LoggingHttpInterceptor, multi: true }
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

运行[角铁测井拦截器堆栈](https://stackblitz.com/edit/angular-ironcore-logging-interceptor)，点击`Make Request`按钮。

拦截器将消息`api/quotes/1 has been intercepted!`记录到控制台。

[![Console Intercepted 1](img/18e9cbac525266064a81f2c83d46af4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m5dFDXzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qjtum3jff6snw2172wdn.png)

## 添加客户端加密

在这一节中，我们将添加一个对`HttpRequest`数据加密和对`HttpResponse`数据解密的`HttpInterceptor`。

实现加密和密钥管理的底层细节并不简单。因此，我们使用经过审核的第三方加密库。

> 对于世界上最好的团队来说，获得正确的安全性是很难的。对于大多数团队来说是不可能的。
> 
> —布鲁斯·施奈尔

我们将使用 IronCore 数据隐私平台。IronCore 允许我们将如何对数据进行分类的决策*(例如，绝密、个人健康信息、个人身份信息)*与谁可以访问这些数据的决策*(例如，西雅图圣恩医院、格雷医生)*。

在您的`AppModule`中将`IronHttpInterceptor`注册为提供商。

```
@NgModule({
  imports: [
    BrowserModule,
    ...
  ],
  declarations: [ AppComponent ],
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: LoggingHttpInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: IronHttpInterceptor, multi: true }
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

运行[angular-iron core-http-interceptor-nothing-encrypted-stack blitz](https://stackblitz.com/edit/angular-ironcore-http-interceptor-nothing-encrypted)，点击`Make Request`按钮。因为`quote`资源没有相关的隐私策略，所以`IronHttpInterceptor`传递请求和响应。

`IronHttpInterceptor`将记录一些消息，表明请求和响应不应该被加密。

[![Console Pre Decrypt 2](img/8931a37ebeea53f423cdb0929a6f5457.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O1R5PwRi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eaw3h2lsu74lnsb8yix7.png)

## 隐私政策

对于我们的例子，我们将定义一个`Order`类。在我们虚构的应用程序中，`Order`是一条绝密信息，我们必须保证它的安全和隐私。REST 端点在(模拟的)后端保存订单。

我们将使用`IronEncrypt`类装饰器来指定我们的数据隐私策略。类装饰器提供了一种将元数据附加到类的便捷方式。如果你用过`@Component`或`@Injectable`，你会对类装饰者的概念很熟悉。

实现`Order`并使用数据分类*绝密*应用`IronEncrypt`类装饰器。

```
@IronEncrypt({dataClassId = 'top-secret'})
export class Order {
    public date = new Date();
    constructor(
        public title: string,
        public message: string,
        public id?: number
    ) {
        this.id = id || Utils.randomInt();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

拦截器会在 HTTP POST 和 PUT 上加密`Order`，在 HTTP GET 上解密。其他数据类型的请求和响应将通过拦截器而不被修改。

[![IronHTTPInterceptor](img/afd434b615dcce75d1929c691ecca148.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DEKPq4Br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uz1tg6pj8degs2oj5afb.jpg)

启动[角铁往返栈](https://stackblitz.com/edit/angular-ironcore-round-trip)提交指令加密。

控制台记录未加密和加密的 JSON。加密的 JSON 在客户机上生成，使用不离开本地设备的私钥。

StackBlitz 将展示如何自动加密`POST`请求和自动解密`GET`请求。

[![Console Post Encrypt 3](img/3bc70879e1f1e213100e3106f89127c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VkMe8Tl5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2rad6sror5umrycdm97b.png)

## 数据分类

> 所有人都有三种生活:公共生活、私人生活和秘密生活。
> 
> 加布里埃尔·加西亚·马尔克斯

大多数应用程序都混合了不同敏感度级别的数据。例如，数据分类可能包括:

*   保护级别*(例如，受限、公共、私有)*
*   监管分类*(例如，个人健康信息、个人身份信息、pci 卡数据)*。
*   职能部门*(如法律、人力资源、财务)*。

在数据隐私架构中，我们希望以声明方式轻松对数据*进行分类。声明式编程是在不描述控制流的情况下指定业务逻辑。声明性方法的一个例子是 CSS 我们定义了一个 CSS 选择器来声明一个段落为粗体，但是我们不需要定义粗体是如何实现的。以声明的方式对数据进行分类简化了数据隐私规则在拥有不同技能的大型开发团队中的一致应用。*

我们希望能够在创建数据时对其进行分类，并在以后决定谁应该能够访问特定的数据分类。

下面是一些如何使用`IronEncrypt`类装饰器来指定数据分类的例子。

```
 // Classify data by protection level
@IronEncrypt({dataClassId = 'private'})
export class IntellectualProperty {
  ...
}

// Classify data by regulatory category
@IronEncrypt({dataClassId = 'personal-health-information'})
export class MedicalRecord {
  ...
}

// Restrict data to a specific group
@IronEncrypt({groupId = 'legal'})
export class BigCompanyContract {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，分类在设计时是已知的。通常情况下，分类更加动态。例如，我们可能希望将活跃用户的数据归类为*个人健康信息*。`IronEncrypt`有许多选项来支持这样的用例。一种简单的方法是在初始化期间定义众所周知的标识符*(例如，个人健康信息)*，作为保存实际代理键的绑定表中的查找键。

```
 // During initialization

this.ironPolicyFactory.bindings.set('personal-health-information', user.phiID);

...

// Using square brackets to indicate an indirect binding...

[IronEncrypt('[personal-health-information]')]
class ... 
```

Enter fullscreen mode Exit fullscreen mode

在本文中，我们将展示如何通过后端 REST 端点将数据分类和策略架构应用于 HTTP。同样的方法也用于定义如何在浏览器本地存储或 S3 桶中加密数据。

## 决定谁可以访问哪些数据

> 计算机科学中的所有问题都可以通过另一种间接方式来解决。
> 
> —大卫·惠勒

访问控制定义了哪些用户或系统进程有权访问数据，以及允许对该数据执行哪些操作。

在大多数系统中，访问控制是由软件算法实现的，这些算法控制对底层数据的访问。只有通过访问控制软件访问数据时，数据才会受到保护。许多隐私和安全事故要么是绕过访问控制层直接访问底层数据的结果，要么是利用复杂的访问控制逻辑中不可避免的错误的结果。

在数据隐私架构中，我们希望访问控制嵌入数据本身。这里重要的一点是，访问控制是由*加密实施的*。加密算法用于从数学上证明只有拥有有效密钥的用户才能根据其角色和权限访问数据。加密实现经过严格审核，并接受开源的审查，以提供更高的质量标准。

在这种数据隐私架构中:

*   数据在原始位置被加密。
*   数据无论存储在何处，都将保持加密状态—云中、设备上、传输中、与合作伙伴共享、备份中、系统日志中等等。
*   数据仅在授权用户或系统进程使用时解密，以加密方式实施访问控制，并且所有操作都作为审计跟踪的一部分进行记录。

为了使该系统具有实用性和可扩展性，将如何对数据*(例如，个人健康信息)*进行分类的决策与谁可以访问该数据的决策分开是很重要的。这是一个叫做[正交接入控制](https://docs.ironcorelabs.com/concepts/orthogonal-access/index.html)的概念。

让我们看一些信息图来解释这个概念。在这里，我们在数据的来源点将数据加密到一个数据分类*(例如，个人健康信息)*。

[![Data Classification 1](img/22e6c14c497b97aedec9ceeb276dc5b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GHoDgh00--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgwokqerm2bxza8cce45.png)

稍后，我们通过加密操作将用户添加到该数据分类或组，来决定谁有权访问该数据分类或组。

[![Data Classification 2](img/c5c02aefb6df6210facf310f44c4bbf9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cYQznYGw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vg2oyeiemdn814gtyedk.jpeg)

还有一个通过从数据分类或组中删除用户来撤销用户访问的加密操作。

[![Data Classification 3](img/197175942bdbcd1d33ceb8d826e6e0b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--flgvCEXL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tw8u7o0mzichbbqyadcy.jpeg)

还有一个加密操作来授予组访问权限和委托管理。

[![Data Classification 4](img/4e9214900de1a9a7a14fca5a8e007d63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kc2ZW9pa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s1jpt0aq6ss9320lvg8.jpeg)

虽然为了有效地使用它，没有必要理解密码支持的正交访问控制是如何工作的，但是许多开发人员想知道。下面我们来探讨一下。

## 工作原理

加密是对数据进行编码的过程，使得除了目标接收者之外的任何人都无法理解数据。在本节中，我们将探讨三种类型的加密:对称加密、非对称加密和转换加密。

在下面的描述中，我们使用术语*明文*和*密文*。

明文是某人想要保护的原始数据，因此除了预定的接收者之外，任何人都不能访问它。

密文是对明文进行加密处理时产生的加密结果。

一个好的加密算法产生看起来像随机数据的密文，并且需要大量的工作来恢复未被授权的任何人的明文。这个从密文恢复原始明文的过程被称为*解密*。

大多数加密算法使用一个*密钥*作为加密/解密过程的一部分。拥有对特定密文正确的密钥就相当于一个人有权访问生成该密文的明文。

### 对称加密

对称加密使用相同的密钥进行加密和解密。它很快而且相对简单，但是用户必须找到一种安全共享密钥的方法，因为它既用于加密也用于解密。

### 非对称加密

不对称加密使用两个在数学上相关的密钥，通常称为密钥对。明文用*公钥*加密，密文用对应的*私钥*解密。不对称加密也称为公钥加密，因为加密密钥可以公开共享，而解密密钥必须保密。

如果数据对多个用户加密，则必须用每个用户的公钥分别加密。要撤销用户访问，我们必须拥有并更改底层数据(及其所有副本)。

[![Asymmetric Public Key Crypto](img/2ac113373db69c4f17d018b9f0e508ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mbB7yOUh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m9k22cs5tk4mxnktgbaw.jpeg)

### 变换加密

转换加密使用两个非对称密钥对(一个用于组，一个用于组成员)和一个从这些密钥对中导出的特殊的*转换密钥*。转换加密用于创建加密支持的访问控制组。

明文使用*组公钥*加密，这是标准的公钥加密。

[![Encrypt to Group](img/e226e09796e724e53f1568213b9fd9a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GFlOm1Rd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ws7kjdjdap2gj5cnwuxv.jpeg)

使用一个*转换密钥*将密文从组密文转换成成员密文。在转换过程中，密文永远不会被解密，从而允许由盲服务来执行转换。

在成员的本地客户机设备上，用成员私钥解密变换后的成员密文。私有解密密钥不会离开成员的设备。

该服务为审计访问提供了一个天然的场所，因为解密数据需要转换。该服务还提供了一个自然的撤销点，因为成员访问需要一个转换密钥。

[![Group to User Transform](img/e53434615a2dd974313558824f40b834.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l3PQxjTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/20ajbrtoi0gjkir7vp28.jpeg)

只有组管理员可以生成转换密钥，该密钥是从组私钥和成员公钥派生的。通过生成和删除变换关键帧，组管理员能够添加和删除组成员。这些操作可以在不需要改变甚至不需要拥有底层数据的情况下执行。

[![Transform Key Generation](img/d9b8552e6b752079d98b82aee404cb01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nCpjsDGP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/230wkaxmr4iijec6vp0y.jpeg)

组管理员无法解密加密到组的数据。这种能够管理组但不能解密底层数据的特性是用密钥扩充算法加密实现的。参见 ACM 论文[密码强制正交访问控制了解详情](https://docs.ironcorelabs.com/cryptography/index.html#acm-paper)。

转换过程不要求(或允许)转换服务在转换密文时解密密文。这允许由半信任的服务来完成转换。该服务永远不会获得对明文的访问，也无法获得任何关于组或成员的私钥的信息。

转换加密的一个重要特性是，可以将数据加密到一个组中，而无需知道该组中有谁，或者谁将被添加或删除。例如，我们可以加密我们的医疗记录，然后决定哪家医院可以访问它们。当我们出院后，我们可以取消访问权限。能够*稍后决定*和*改变我们的想法*使 transform encryption 成为保护云中数据的理想选择，在云中，数据通常由大量或动态的用户共享。

> 计算机科学只有两个硬东西:缓存失效和事物命名。
> 
> —菲尔·卡尔顿

在学术文献中，变换加密被称为[代理再加密](https://en.wikipedia.org/wiki/Proxy_re-encryption) (PRE)。我们不使用这个术语，因为在其他上下文中，重新加密意味着加密-解密-加密循环，这是我们*不*在这里做的。我们使用术语[转换加密](https://docs.ironcorelabs.com/concepts/transform-encryption/index.html)来强调服务将组密文转换成成员密文，而不需要中间的解密步骤。

## 信封加密

在上面的部分中，我们通过假设对整个数据流应用单一类型的加密来简化我们的描述。实际上，公钥加密几乎总是使用对称的内部密钥(称为文档加密密钥(DEK ))来加密数据。

在加密过程中，会生成一个随机 AES 密钥作为文档加密密钥(DEK)。数据用这个密钥加密。

[![Encrypt Document](img/7087e5224f0ea4aefa5adec5711eea13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bk3t4V72--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yh3tz789uu3v4v7x7aom.jpeg)

然后用公钥对 DEK 进行加密。加密的 DEK 可以与数据一起存储或存储在其他地方。

[![Encrypt Document Key](img/14e09a0c95c55327980cd7d331ac4b8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XA02RrRP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a19nmujhuvrf2ucyu1wn.jpeg)

在转换加密中，该服务将加密的 DEK 从组转换为用户。转换密钥用于执行“DEK 加密到组”到“DEK 加密到用户”的映射，DEK 从不解密。

[![Transform Encrypted DEK](img/629dc3cb2b607abd1725fbd16fb51399.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oWnvHJZJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dyopdznz0ikkvt90dqpb.jpeg)

在我们转换了加密的 DEK 之后，用户的私钥被用来恢复 DEK 对称密钥。

[![Decrypt Encrypted DEK](img/d3b2866f34b09edc4fa07c0929a7f642.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t7U_Sq35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zcdt3zahc161ldl467bs.jpeg)

然后，未加密的 DEK 用于解密底层数据。

[![Decrypt Encrypted Document](img/e508c230b0b677b76d5c8b3c7d2d2929.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YhWK1uV8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2dpqkpg6kjagzwya13ks.jpeg)

加密的 DEK 很小，最大限度地减少了网络流量，使转换成为轻量级操作。由于 DEK 是对称密钥，解密速度更快。

## 未来话题

我们跳过了一些与数据隐私相关的重要话题。我们将在以后的文章中讨论这些内容。如果你有兴趣跟随，观看这个博客和/或注册我们的简讯。我们计划的一些主题:

*   管理用户密钥
*   端点安全性
*   身份管理和数据隐私
*   S3 的数据隐私
*   使用 GraphQL 保护数据隐私
*   Redux 和 NgRx 存储的数据隐私

## 下一步

完整的示例应用程序可在[https://github.com/IronCoreLabs/getting-started-angular](https://github.com/IronCoreLabs/getting-started-angular)获得。

在[https://admin.ironcorelabs.com](https://admin.ironcorelabs.com)注册免费开发者账户，了解更多关于 IronCore 隐私平台如何简化高级数据隐私架构的实施。