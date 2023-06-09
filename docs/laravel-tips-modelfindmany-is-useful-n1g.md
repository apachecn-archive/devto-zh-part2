# Laravel 提示:Model::findMany 很有用

> 原文：<https://dev.to/acro5piano/laravel-tips-modelfindmany-is-useful-n1g>

当你想用 ID 的数组得到`find`的多个结果时，可以把数组传递给`Model::find`。

这个特性从 Larave 5.2 开始被记录，但是我发现我们甚至可以在 Laravel 5.1 中使用它。我读了 Laravel 的源代码(没有目标)，然后找到了它。

```
>>> App\User::find([1,2,3])

=> Illuminate\Database\Eloquent\Collection {#1090
     all: [
       App\User { id: 1 },
       App\User { id: 2 },
       App\User { id: 3 },
     ],
   } 
```

Enter fullscreen mode Exit fullscreen mode

在我发现这个技巧之前，我曾经像`User::whereIn([1, 2, 3])->get()`一样写作。

我们也可以用`findMany`的方法。实际上，如果第一个参数是数组，`find`方法运行`findMany`。

在`laravel/framework/src/Illuminate/Database/Eloquent/Builder.php`中

```
public function find($id, $columns = ['*'])
{
    if (is_array($id) || $id instanceof Arrayable) {
        return $this->findMany($id, $columns);
    }
    return $this->whereKey($id)->first($columns);
}

public function findMany($ids, $columns = ['*'])
{
    if (empty($ids)) {
        return $this->model->newCollection();
    }

    $this->query->whereIn($this->model->getQualifiedKeyName(), $ids);

    return $this->get($columns);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/laravel/framework/blob/ba 0fa 733243 FD 9 b 5 ABAC 67 e 40 c 9 a1 f 7 ba 2 ca 0391/src/Illuminate/Database/口才/Builder.php#L287](https://github.com/laravel/framework/blob/ba0fa733243fd9b5abac67e40c9a1f7ba2ca0391/src/Illuminate/Database/Eloquent/Builder.php#L287)

所以我们可以像`User::findMany([1, 2, 3])`一样使用这个方法。
我个人比较喜欢这种方式，因为更显而易见。