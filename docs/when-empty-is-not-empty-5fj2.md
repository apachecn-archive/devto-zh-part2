# ★空时不空

> 原文：<https://dev.to/freekmurze/when-empty-is-not-empty-5fj2>

最近当我在做一个项目时，我在使用`empty`函数时得到了一些奇怪的结果。这是我调试的东西。我将代码简化了一点，与您分享。

```
var_dump(
   $person->firstName, 
   empty($person->firstName)
); 
```

结果是这样的:

```
string(5) "Freek"
bool(true) 
```

这真的很奇怪。一个变量怎么能同时容纳一个字符串和空的呢？让我们试试`$person->firstName` :
上的其他几个功能

```
var_dump(
   $person->firstName, 
   empty($person->firstName), 
   isset($person->firstName), 
   is_null($person->firstName)
); 
```

结果:

```
string(5) "Freek"
bool(true) // empty
bool(true) // isset
bool(false) // is_null 
```

`isset`和`is_null`的行为符合预期，只有`empty`返回错误的结果。

让我们来看看`Person`类的实现。

```
class Person
{
    protected $attributes = [];

    public function __construct(array $attributes)
    {
        $this->attributes = $attributes;
    }

    public function __get($name)
    {
        return $this->attributes[$name] ?? null;
    }
} 
```

在这里，您可以看到使用神奇的`__get()`方法从`$attributes`数组中检索到了`Person`对象的属性。

当将变量传递给普通函数时，将首先计算`$person->firstName`。评估的结果将被传递给函数。

但是`empty`不是一个函数，它是一个语言结构。所以你传递给它的东西不会先被求值。当将`$person->firstName`传递给它时，它将检查`$person`上的`firstName`属性的内容。由于该属性不存在，它将返回`false`。

如果你想让`empty`函数在这种情况下工作，你需要实现神奇的`__isset`方法。

```
class Person
{
    protected $attributes = [];

    public function __construct(array $attributes)
    {
        $this->attributes = $attributes;
    }

    public function __get($name)
    {
        return $this->attributes[$name] ?? null;
    }

    public function __isset($name)
    {
        $attribute = $this->$name;

        return !empty($attribute);
    }
} 
```

当`empty`完成它的工作时，它会用那种神奇的方法来决定结果。

让我们再次尝试转储:

```
var_dump(
   $person->firstName, 
   empty($person->firstName)
); 
```

新结果:

```
string(5) "Freek"
bool(false) 
```

完美！