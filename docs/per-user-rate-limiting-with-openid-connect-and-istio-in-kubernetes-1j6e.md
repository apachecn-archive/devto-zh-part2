# Kubernetes 中 OpenID connect 和 Istio 的每用户速率限制

> 原文：<https://dev.to/danlebrero/per-user-rate-limiting-with-openid-connect-and-istio-in-kubernetes-1j6e>

*本文原载于 [Akvo 的博客](https://akvo.org/blog/per-user-rate-limiting-with-openid-connect-and-istio-in-kubernetes/)*

为了确保我们的每个合作伙伴都能够使用 Akvo 的 API，我们需要确保没有人能够滥用它。我们希望确保每个合作伙伴都能公平地分享服务器资源。

在 HTTP APIs 的情况下，这通常意味着限制合作伙伴可以发出请求的速率。执行速率限制的系统需要:

*   确定是谁发出了 HTTP 请求。
*   统计每个用户发出了多少请求。
*   一旦用户耗尽其配额，则拒绝任何用户请求。

有很多开源产品和库可供选择，但是我们决定尝试一下。

对于这样的任务，Istio 有点手重。然而，由于 Istio 是一个[服务网格](http://philcalcado.com/2017/08/03/pattern_service_mesh.html)，它还提供路由、负载平衡、蓝/绿部署、金丝雀释放、流量分流、断路器、超时、网络故障注入和遥测。此外，它还提供[内部 TLS 加密](https://istio.io/docs/concepts/security/mutual-tls.html)和[基于角色的访问控制](https://istio.io/docs/concepts/security/rbac.html)，鉴于我们对[即将到来的 GDPR 立法](https://akvo.org/blog/gdpr-becoming-compliant/)的承诺，这对我们来说非常重要。

## 识别用户

Akvo 的 API 已经使用了 [OpenID 连接标准](https://github.com/akvo/akvo-flow-api/wiki/Authentication)，Istio 附带了一个方便的 [JWT 认证过滤器](https://github.com/istio/proxy/tree/master/src/envoy/http/jwt_auth)，所以我们只需要配置过滤器指向我们的 OpenID 提供者:

```
apiVersion: config.istio.io/v1alpha2
kind: EndUserAuthenticationPolicySpec
metadata:
  name: flow-api-auth-policy
  namespace: default
spec:
  jwts:
    - issuer: https://kc.akvotest.org/auth/realms/akvo
      jwks_uri: https://kc.akvotest.org/auth/realms/akvo/protocol/openid-connect/certs 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要告诉 Istio 将认证规范应用到我们的后端服务:

```
apiVersion: config.istio.io/v1alpha2
kind: EndUserAuthenticationPolicySpecBinding
metadata:
  name: flow-api-auth-policy-binding
  namespace: default
spec:
  policies:
    - name: flow-api-auth-policy
      namespace: default
  services:
    - name: flow-api
      namespace: default 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果请求中存在 JWT 访问令牌，Istio 将对其进行验证，并将主体添加到请求中，但是如果没有令牌，请求仍然会通过。

## 强制执行用户

鉴于对 API 的任何访问都必须使用访问令牌来完成，我们可以添加一个策略规则来实施它。要配置策略，我们需要:

一个处理器，在这个特殊的例子中是一个[丹尼尔适配器](https://archive.istio.io/v0.6/docs/reference/config/adapters/denier)，它将返回一个 401:

```
apiVersion: "config.istio.io/v1alpha2"
kind: denier
metadata:
  name: flow-api-handler
  namespace: default
spec:
  status:
    code: 16
    message: You are not authorized to access the service 
```

Enter fullscreen mode Exit fullscreen mode

一个实例，在本例中是一个 [Check Nothing](https://archive.istio.io/v0.6/docs/reference/config/template/checknothing) 模板，因为处理程序不需要数据:

```
apiVersion: "config.istio.io/v1alpha2"
kind: rule
metadata:
  name: flow-api-deny
  namespace: default
spec:
  match: destination.labels["run"] == "flow-api" && (request.auth.principal|"unauthorized") == "unauthorized"
  actions:
  - handler: flow-api-handler.denier.default
    instances: [flow-api-denyrequest.checknothing.default] 
```

Enter fullscreen mode Exit fullscreen mode

如果您不熟悉处理程序、实例或规则概念，请参见 [Istio 文档](https://archive.istio.io/v0.6/docs/concepts/policy-and-control/mixer-config)。

## 计数用法

首先，我们需要定义我们想要计数的内容:

```
apiVersion: config.istio.io/v1alpha2
kind: quota
metadata:
  name: requestcount
  namespace: istio-system
spec:
  dimensions:
    destination: destination.labels["run"] | destination.service | "unknown"
    user: request.auth.principal|"unauthorized" 
```

Enter fullscreen mode Exit fullscreen mode

我们使用两个维度，用户和目的地服务，这样我们可以对不同的后端服务有不同的限制。

进行实际计数:

```
apiVersion: config.istio.io/v1alpha2
kind: QuotaSpec
metadata:
  name: flow-api-quota
  namespace: default
spec:
  rules:
    - quotas:
        - quota: requestcount.quota.istio-system
          charge: 1 
```

Enter fullscreen mode Exit fullscreen mode

Istio 速率限制让您可以灵活地对执行成本可能更高的请求收取更多费用，但在我们的案例中，我们决定对所有请求一视同仁。

最后，我们需要将计数与后端服务连接起来:

```
apiVersion: config.istio.io/v1alpha2
kind: QuotaSpecBinding
metadata:
  name: flow-api-quota-binding
  namespace: default
spec:
  quotaSpecs:
    - name: flow-api-quota
      namespace: default
  services:
    - name: flow-api
      namespace: default 
```

Enter fullscreen mode Exit fullscreen mode

## 强制执行使用配额

现在我们知道了你是谁，如何计数，我们需要定义什么是合理的用法。我们通过一个[内存配额适配器](https://archive.istio.io/v0.6/docs/reference/config/adapters/memquota) :
来做到这一点

```
apiVersion: config.istio.io/v1alpha2
kind: memquota
metadata:
  name: handler
  namespace: istio-system
spec:
  quotas:
  - name: requestcount.quota.istio-system
    maxAmount: 60
    validDuration: 10s
    overrides:
    - dimensions:
        destination: flow-api
      maxAmount: 20
      validDuration: 10s 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们允许每个用户每秒最多有十个请求，除非请求发送到流 API，在这种情况下，我们允许每秒最多有两个请求。

请注意，在生产中，您将希望使用 [Redis 配额](https://archive.istio.io/v0.6/docs/reference/config/adapters/redisquota)而不是内存配额，因为内存配额是短暂的，并且对于 Mixer 实例是本地的。

最后，我们创建一个策略规则，将配额与计数器连接起来:

```
apiVersion: config.istio.io/v1alpha2
kind: rule
metadata:
  name: quota
  namespace: istio-system
spec:
  actions:
  - handler: handler.memquota
    instances:
    - requestcount.quota 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

现在，我们可以检查一切是否如预期的那样工作，并且没有用户能够滥用系统。为了进行测试，我们将配额改为每三秒钟一个请求。结果如下:

你可以在这里找到测试脚本[的版本，在这里](https://github.com/akvo/akvo-platform/blob/issue-57/rate-limit-istio/istio/simple-check.sh)找到[上面的所有代码。](https://github.com/akvo/akvo-platform/tree/b17ed526fa48d22e7ef7a2f37195302119309dca/istio/k8s)

## 我们完了吗？

Istio 使我们能够确保我们所有的合作伙伴获得公平的资源份额，只需要一点点配置，而不必修改或更改我们现有的任何代码，这是一个很大的优势。

但限速只是让 Akvo 平台更加稳定的一部分。Istio 还提供了很多更好的东西来增加稳定性，并使其更加安全，我们肯定会在不久的将来进行调查。