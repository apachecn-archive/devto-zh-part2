# Go 界面

> 原文：<https://dev.to/chuck_ha/interfaces-in-go-3onc>

Go 里的界面真的很棒。它们帮助我们获得良好的测试覆盖率，并使我们的代码的依赖性显而易见，因此更容易阅读。

让我们看看一些现有的代码，看看我们如何改变它，使依赖关系显式。

这里需要重点关注的是，这个函数不带参数，而是调用导入的函数，比如`glog`、`http`和`netutil`。

```
// Check validates http connectivity type, direct or via proxy.  func  Check()  (warnings,  errors  []error)  {  glog.V(1).Infof("validating if the connectivity type is via proxy or direct")  u  :=  (&url.URL{Scheme:  hst.Proto,  Host:  hst.Host}).String()  req,  err  :=  http.NewRequest("GET",  u,  nil)  if  err  !=  nil  {  return  nil,  []error{err}  }  proxy,  err  :=  netutil.SetOldTransportDefaults(&http.Transport{}).Proxy(req)  if  err  !=  nil  {  return  nil,  []error{err}  }  if  proxy  !=  nil  {  return  []error{fmt.Errorf("Connection to %q uses proxy %q. If that is not intended, adjust your proxy settings",  u,  proxy)},  nil  }  return  nil,  nil  } 
```

([来源](https://github.com/kubernetes/kubernetes/blob/d9506299ac8bc163e2e3f5131387f226fcbc64d5/cmd/kubeadm/app/preflight/checks.go#L443))

我们来看函数的第一行:`glog.V(1).Infof(...)`。这里我们真正关心的是我们可以调用`Infof`。我们可以定义一个完全满足该属性的类型:

```
type  logger  interface  {  Infof(string,  ...interface{})  } 
```

现在我们可以让我们功能只依赖于接口。

```
func  Check(log  logger)  (warnings,  errors  []error)  {  ...  } 
```

并且我们可以用`log.Infof`代替对`glog.V(1).Infof`的调用。

```
type  logger  interface  {  Infof(string,  ...interface{})  }  func  Check(log  logger)  (warnings,  errors  []error)  {  log.Infof("validating if the connectivity type is via proxy or direct")  ...  }  func  main()  {  // glog.V(1) is now merely an implementation detail. This function accepts anything that implements our interface!  Check(glog.V(1))  } 
```

我们刚刚经历的过程是这样的:

1.  看看我们希望能够控制的依赖性。
2.  将行为提取到一个接口中。
3.  将该接口传递给函数。
4.  将函数调用替换为对传入的接口的方法调用。
5.  将我们定义的接口的实现传递到函数调用中。

我们可以对我们想要的任何其他隐式依赖重复这一过程。下面是一个更复杂的重构，它将`http.NewRequest`包装在一个结构中，该结构实现了我们创建的接口，该接口精确地定义了我们需要的行为。

```
type  requestBuilder  interface  {  NewRequest(string,  string,  io.Reader)  (*http.Request,  error)  }  type  reqBuilder  struct  {}  func  (rb  *reqBuilder)  NewRequest(method,  url  string,  body  io.Reader)  (*http.Request,  error)  {  return  http.NewRequest(method,  url,  body)  }  func  Check(rb  requestBuilder)  (warnings,  errors  []error)  {  ...  req,  err  :=  rb.NewRequest("GET",  u,  nil)  if  err  !=  nil  {  return  nil,  []error{err}  }  ...  }  func  main()  {  Check(&reqBuilder{})  } 
```

为什么我们要经历这么多麻烦？嗯，在这种情况下我们真的不会，但这要看情况而定。如果错误案例非常复杂，并且我们需要测试从`NewRequest`返回的特定错误，那么这对我是有意义的。但是如果你只关心测试`err != nil`，那么阅读 [`http.NewRequest`源代码](https://golang.org/src/net/http/request.go?s=25278:25347#L759)并找出如何从那个函数中产生一个错误要容易得多。

最后一个依赖项`netutil`，留给读者作为练习。

另一个需要注意的非常重要的事情是接口是在调用点定义的。它们不是进口的。我们不导入接口，因为我们导入的接口很可能比我们实际需要的要大。我们的接口应该是我们所寻求的行为，仅此而已。