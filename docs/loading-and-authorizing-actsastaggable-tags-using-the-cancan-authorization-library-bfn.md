# 使用 Cancan 授权库加载和授权 ActsAsTaggable 标记

> 原文：<https://dev.to/husteadrobert/loading-and-authorizing-actsastaggable-tags-using-the-cancan-authorization-library-bfn>

TL；(太长了，博士没看)

```
class SomeController < ApplicationController
  load_and_authorize_resource class: ActsAsTaggableOn::Tag

  def index; end  #@tags is available in this action
  def show; end #@tag is available in this action
end 
```

解释:

Cancan 库对于控制资源以及允许用户读取、写入、修改或更改资源非常有用。通常，当 load_and_authorize_resource 包含在 RESTful 风格的控制器中时，它使用 before 过滤器将实例变量加载到内存中。然而，如果您想从另一个类加载资源，或者如果您想使用的模型与控制器的命名空间不同，事情就变得困难了。

如果您使用 ActsAsTaggableOn 向应用程序中的某些模型添加标签，您仍然可以使用上面的代码将这些标签作为资源加载。来自 github 上的 Cancan 文档([https://github . com/ryanb/Cancan/wiki/authoring-controller-actions # custom-class](https://github.com/ryanb/cancan/wiki/authorizing-controller-actions#custom-class))
“如果模型类的命名空间与控制器不同，您需要指定:class 选项。”

当使用 Cancan 时，记得设置你的用户能力来访问你的能力文件中的标签！

祝你好运，并快乐编码！