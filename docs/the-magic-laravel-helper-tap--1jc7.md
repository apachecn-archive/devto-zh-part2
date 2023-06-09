# 神奇的 Laravel 助手点击()

> 原文：<https://dev.to/simo_benhida/the-magic-laravel-helper-tap--1jc7>

你好，

在本帖中，我们将讨论`tap()` helper 是如何工作的，以及我们可以在项目中的什么地方使用它。

首先来说说`tap()`到底在幕后做些什么。

```
<?php
function tap($value, $callback = null)
    {
        if (is_null($callback)) {
            return new HigherOrderTapProxy($value);
        }

        $callback($value);

        return $value;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在使用了`tap()`之后，你必须知道我们传递的第一个东西总是要返回给我们的。

让我们来看一个例子，这样我们就能更多地看到不同之处。现在让我们假设`$callback`不为空，我们的`tap()`助手也是这样。

```
<?php
function tap($value, $callback)
    {
        $callback($value);

        return $value;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们的例子是，我们想要更新一个用户，同时返回编辑过的`$user`。

```
<?php
public function update(Request $request,User $user)
{
   $user = $user->update($param);//return boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

我们都知道`update()`返回`boolean`，但是我们希望它返回我们编辑过的`$user`，这样我们就可以把它传递给我们的 json 响应。`tap()`的规则来了

```
<?php
public function update(Request $request,User $user)
  {
     $user = tap($user,function($user) use($request){
         $user->update($request->all());
        }); //returns Edited User
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们通过了接受`$user`并运行`update()`方法的`$user`和`closure`。

`$callback($value);`将被触发，它将在最后返回`$value`，在我们的例子中，它返回`$user`模型。

但是这没有用，看起来很恶心，因为我们需要将$request 传递给闭包，并添加额外的两行。

所以这里出现了一个空的`$callback`规则，它将返回一个`new HigherOrderTapProxy($value);`来帮助我们解决上面的问题。

让我们一起来了解一下`HigherOrderTapProxy($value)`到底是做什么的。

```
<?php

namespace Illuminate\Support;

class HigherOrderTapProxy
{

    public $target;

    public function __construct($target)
    {
        $this->target = $target; //target in our case is $user model
    }

    //when we call a method it will trigger this function
    public function __call($method, $parameters) 
    {
        $this->target->{$method}(...$parameters); //$user->update($parameters)

        return $this->target; //return $user;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像我们看到的`HigherOrderTapProxy`类接受`$value`,每次我们调用这个类中的方法时，它都会触发`__call()`函数，然后这个方法会应用到传递的`$value`,然后它会返回它。

就像你在上面的评论中看到的。现在，如果你真的明白`HigherOrderTapProxy`是如何工作的，你可以这样做，它就会工作。

```
<?php
public function update(Request $request,User $user)
 {
   $user = tap($user)->update($request->all());//return edited $user
 } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在一行代码中有相同的值。

我希望你喜欢阅读这篇文章。