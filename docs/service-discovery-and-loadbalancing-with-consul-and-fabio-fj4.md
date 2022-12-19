# Consul 和 Fabio 的服务发现和负载平衡

> 原文：<https://dev.to/adelowo/service-discovery-and-loadbalancing-with-consul-and-fabio-fj4>

在当今的云中，应用程序或服务(如果你愿意的话)通常分布在一个
机器集群中，并带有多个 IP 地址和端口号，这些 IP 地址和端口号很难
记住，尤其是考虑到当今大多数软件最终都拥有动态 IP 这一事实。拆除
并重启相同的服务很可能意味着它有一个更新的 IP 地址。

那么应用程序如何知道它需要与之通信的其他支持服务呢？这就是
***服务发现*** 发挥作用的地方。基本上，服务发现是服务交流关于自身信息(IP 地址、端口号、元数据？？？)给别人。它还涉及到在这个服务上运行健康检查
来检查可用性，并将其传达给其他人。

这方面的一个流行应用(至少在 Golang 社区)是 Consul。当我在我工作的地方写了一篇关于使用 consul 的博文时，它总是[用于配置在不同机器](https://lanre.wtf/blog/2018/02/18/managing-production-configuration)
上运行的应用程序，所以我们不必处理标志、`env`值和所有这些。但是领事远不止于此。键值存储
是它做的事情之一。

#### 使用 consul 进行服务发现或为 consul 寻找其他用例的灵感来自哪里？

所以我们有了这个非常小的应用程序，姑且称之为`monolith`。我们旋转了 2 个液滴，在那里运行了二进制程序，
得到了一个负载平衡器，然后我们就去睡觉了。但是 monolith 在架构决策方面相当混乱
,因为它是在一次疯狂的冲刺中写成的，我们只是想尽快推出一些东西。我们达到了 10k
用户，我们的移动应用出现了 ***一些问题***——6 个月后，我们发现由于我们的人口统计数据，我们可以把应用做得更好
(实际上更简单)。由于移动的家伙们正在做他们的事情，我的团队于是决定利用
这个机会 ***重写*** 我们遇到的混乱——当时是< = 15k LOC。

考虑到 monolith 的内存使用量<= 【T0】 on a 1gb droplet, we put 【T1】 on the same server.
酷吗？但是刚刚出现了一个问题，负载平衡器将流量重定向到一个静态端口，但是现在我们
让`monolith`和`v2`在`:9001`和`:9002`并排运行，但是流量只会到达`:9001`
而不是`:9002`。在这个阶段，唯一有意义的是某种形式的 API 网关，这样我们就可以根据前缀
重定向流量，`/v1`转到`monolith`，`v2`转到`v2`。我写了一些快速代理来实现这一点，但这意味着
每次我们添加新服务器时，我们都需要更新负载平衡器的目标服务器，并将代理和
应用程序部署到新服务器。

### 一路服务发现

我想要一种方法来添加新的服务器，在那里运行应用程序，而不是手动更新一些列表或部署
不必要的服务。

我环顾四周，发现法比奥和其他人就像他们说的那样已经成为历史了。

### 在领事处注册服务

我在 Github 上做了一个简单的演示[..我们首先需要连接领事..这样做很容易](https://github.com/adelowo/service-discovery-demo) 

```
func New(addr string) (*Client, error) {
    conf := consul.DefaultConfig()
    conf.Address = addr

    return NewWithConfig(conf)
} 
```

Enter fullscreen mode Exit fullscreen mode

实际注册服务的部分是这个

```
func (c *Client) RegisterService(svc *consul.AgentServiceRegistration) (string, error) {

    id := uuid.New()
    svc.ID = id

    // We need to return the id, so the app can delete itself from beinng discovered..
    // Remember that the service discovery db is now our source of truth,
    // so we need to clean up when neccessary
    return id, c.inner.Agent().ServiceRegister(svc)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Deregistering the service is pretty easy,
// just pass the id gotten after a successful registration
// This should ideally be done as part of the shutdown process.
func (c *Client) DeRegister(id string) error {
    return c.inner.Agent().ServiceDeregister(id)
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，这一切如何适应真实的应用程序/服务呢？

*   呼叫`Register`
*   添加健康检查
*   关机时取消注册。

我们来看一个例子:

```
package main

import (
    "flag"
    "fmt"
    "log"
    "net/http"
    "strconv"

    "github.com/adelowo/service-discovery-demo/pkg/registry"
    "github.com/hashicorp/consul/api"
)

func main() {

    var discoveryURL = flag.String("discovery", "127.0.0.1:8500", "Consul service discovery url")
    var httpPort = flag.String("http", ":3000", "Port to run HTTP service at")

    flag.Parse()

    reg, err := registry.New(*discoveryURL)
    if err != nil {
        log.Fatalf("an error occurred while bootstrapping service discovery... %v", err)
    }

    var healthURL string

    // look at the code put on Github for this
    ip, err := registry.IPAddr()
    if err != nil {
        log.Fatalf("could not determine IP address to register this service with... %v", err)
    }

    healthURL = "http://" + ip.String() + *httpPort + "/health"

    pp, err := strconv.Atoi((*httpPort)[1:]) // get rid of the ":" port
    if err != nil {
        log.Fatalf("could not discover port to register with consul.. %v", err)
    }

    svc := &api.AgentServiceRegistration{
        Name:    "cool_app",
        Address: ip.String(),
        Port:    pp,
        Tags:    []string{"urlprefix-/oops"},
        Check: &api.AgentServiceCheck{
            TLSSkipVerify: true,
            Method:        "GET",
            Timeout:       "20s",
            Interval:      "1m",
            HTTP:          healthURL,
            Name:          "HTTP check for cool app",
        },
    }

    id, err := reg.RegisterService(svc)
    if err != nil {
        log.Fatalf("Could not register service in consul... %v", err)
    }

    http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
        r.Body.Close()
        w.WriteHeader(http.StatusOK)
        w.Write([]byte("OK"))
    })

    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        r.Body.Close()
        fmt.Println("Here")
        w.Write([]byte("home page"))
    })

    if err := http.ListenAndServe(*httpPort, nil); err != nil {
        reg.DeRegister(id)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行这个代码的多个版本(使用不同的 IP)应该会给你一个界面(在 consul 中)
如下所示:

[![All services](../Images/b3c3ce6d03c6cf3f43d71aef31b4ba54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4txib7_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lanre.wtf/img/log/allservices.png)
[![Deeper view of all services](../Images/0b6bdd3ceb68598be456d576b2520f60.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s---0xdsEnO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lanre.wtf/img/log/running_services.png)

### 那么负载平衡器在这一切中扮演什么角色呢？

既然我们有同一个服务的多个实例在运行，我们如何唯一地挑选其中一个(实际上是一个健康的实例
)呢？原来负载平衡是 consul 已经内置的一个特性，但是我们不会使用它，因为它没有
基于 ***权重/优先级*** 的负载平衡，而且它把你的应用程序和 consul 联系得太紧密了..

法比奥进来了。它需要做的就是向领事
注册你的服务，并为他们提供健康检查。它支持 TCP、HTTP 和 Websockets。

它读取咨询服务，解析这些服务的标签/位置(地址加端口),并使用这些解析的标签建立路由表。

回头看看我们的小服务，您会注意到`Tags: []string{"urlprefix-/oops"},`，这实际上是`fabio`需要
将所有以`/oops`开始的请求路由到我们服务的健康实例。通过指定主机名`urlprefix-mysite.com/`可以变得更有趣。

运行`fabio`实际上是你所需要的，因为没有配置是 ***需要的*** ，它的默认设置
非常好，尽管你总是可以微调到你想要的。

Fabio 有一个 web ui，在`:9998`上公开，负载平衡器在`:9999`上公开。因此，理想情况下，你所有的流量都将被发送到
`:9099`，并让法比奥计算出转发到哪里。例如，要访问我们服务的`/`路线，我们必须执行
`curl -X GET http://localhost:9999/oops`

运行上面的服务的 3 个实例，fabio 产生下面的 UI

[![Fabio](../Images/b1c273de881ce4d2f64c9605561118be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O1xHC2Vh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lanre.wtf/img/log/fabio.png)

> 你显然不想让`:9998`暴露在外面工作..一个可以用来查看它的技巧是 ssh 隧道。
> `ssh -N -f -L 9998:localhost:9998 lanre@IPadress`...立即在您的电脑上访问`localhost:9998`

有了这个，所有需要做的就是将服务部署到另一个服务器上，它将向 consul 注册它自己，fabio
然后将它添加到它的路由表中。