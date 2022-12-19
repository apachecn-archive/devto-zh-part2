# 使用 Laravel 进行活性和就绪性探测

> 原文：<https://dev.to/pgoodjohn/liveness-and-readiness-probes-with-laravel-5d65>

*这篇文章最初发表在我的[博客](http://bit.ly/laravel-k8s-probes)上，当我第一次发表的时候，你可以看到我的文章，来看看吧！*

活性和就绪探测器是 Kubernetes 套件为编排容器提供的两个最强大和最有用的工具。Kubernetes 本身非常擅长重新安排部署、重新启动 pod，以及一般情况下处理极端情况。默认配置所缺乏的是一种解决在其 pod 中运行的应用程序的状态问题的方法:假设一个应用程序正在 pod 中运行，由于死锁，它不能再提供任何响应。您的普通 Kubernetes 配置无法检测到这样的问题，因此如果您认为这种情况可能发生在您的应用程序中，您将需要设置一些工具来避免它。幸运的是，*活性探测器*正是为了解决这个问题而设计的。让我们看看如何在您的 Laravel 应用程序中实现一个简单的活跃度探测和就绪性探测。

首先，您需要建立一个`health`路由，一个简单的路由，除了返回一个`200 OK`状态代码之外什么也不做。您可以通过简单地将下面的代码片段添加到您的`routes.php` :
中来实现这一点

```
Route::get('/healthz', function () {
    return 'ok';
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦您将这个简单的路线添加到代码中，您将需要将下面的代码片段添加到您的`deployment.yml` :

```
livenessProbe:
httpGet:
    path: /healthz
    port: 80
initialDelaySeconds: 15
failureThreshold: 3
periodSeconds: 3 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段将告诉 Kubernetes 每隔`periodSeconds`秒对指定的`path`执行一次请求。如果响应的状态代码在`200`和`400`之间，检查将被记录为成功，任何其他状态代码将被记录为失败。在`failureThreshold`连续检查失败后，K8s 会杀死 pod 并启动一个新的。最后，`initialDelaySeconds`指示 Kubernetes 在开始发送活跃度探测检查请求之前将等待多少秒:这将为应用程序提供启动和准备服务请求的时间。

另一方面，当一个 pod 不能接收流量，但它的 HTTP 请求已经在工作时，准备就绪探测有助于禁用它。假设应用程序需要访问数据库来返回数据:我们可以说，如果应用程序不能访问数据库，它就没有准备好为流量服务。
第二种类型的探测器有助于准确检查:如果检查失败，Kubernetes 会将探测器标记为未准备好接受流量，它将不会暴露给来自集群外部或内部的流量。

让我们看看如何实现一个准备就绪探测器，从我们的应用程序需要访问一个数据库以被认为“准备就绪”的需求开始。首先，让我们创建一个`/readiness` HTTP 端点:这个路由将对数据库执行一个简单的检查，如果它能够计算数据库中一个`check`表的内容，它将返回一个`200 OK`状态代码，否则将返回一个`500`状态代码。

```
Route::get('/readiness', function () {
    $check = DB::table('check')->count();
    if ($check > 0) {
        return 'ok';
    }
    return response('', 500);
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了工作路线，我们将需要添加下面的代码片段到我们的`deployment.yml`并重新部署我们的应用程序。

```
readinessProbe:
httpGet:
    path: /readiness
    port: 80
initialDelaySeconds: 10
successThreshold: 1
failureThreshold: 3
timeoutSeconds: 1
periodSeconds: 15 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，活性和就绪性探测的语法非常相似，这里唯一的额外字段是`successThreshold`参数，它指示需要多少次成功的检查才能将 pod 视为“就绪”。例如，在上述情况下，在一次成功的检查之后，应用程序将准备就绪，并开始接收流量。

正如您所看到的，为您的应用程序(Laravel 或其他)实现活性和就绪性探测非常简单:只需添加一个路由，该路由返回 200 到 400 之间的状态代码(表示检查成功),任何其他状态代码(表示失败),然后将上述代码片段添加到您的部署文件中，您就可以开始运行了。显然，像任何其他 Kubernetes 元素一样，活性和就绪性探针可以更深入地配置。为了探索如何做到这一点，我建议你查看一下官方文档，它总是非常全面。