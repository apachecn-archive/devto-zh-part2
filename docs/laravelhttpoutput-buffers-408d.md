# Laravel 的 HTTP 测试中输出缓冲器未关闭的错误

> 原文：<https://dev.to/dala00/laravelhttpoutput-buffers-408d>

在 Laravel 的测试中，不知不觉中就可以进行 Risky 测试了。 因为不是失败，所以在 CI 上好像什么也没说，因为在运行时也没有发生错误，所以好像没有注意到。

具体是以下这样的错误。

```
Test code or tested code did not (only) close its own output buffers 
```

Enter fullscreen mode Exit fullscreen mode

从结论上来看，好像是因为使用了以下模板的写法，所以没有正常回复。

```
@section('additionallTag', $data->tag) 
```

Enter fullscreen mode Exit fullscreen mode

这一点我觉得记述本身没有问题，但是因为变量中放入的是 HTML 标签，所以好像发生了这次的错误。 因此，经过如下修改后得到了改善。

```
@section('additionalTag')  
{!! $data->tag !!}  
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

也找到了解说同样错误的改善方法的报道。

[在 Laravel 的 HTTP 测试中不知为何没有回复 response 的事情](https://qiita.com/t_k_m_s_otya/items/6e973f90c09df40f2224)

这种情况是因为 section 没有被关闭，但是像这次这样根据变量的指定可能无法很好地关闭。