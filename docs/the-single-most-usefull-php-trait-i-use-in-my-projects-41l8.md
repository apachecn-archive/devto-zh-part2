# 我在项目中使用的最有用的 php 特性

> 原文：<https://dev.to/sadick/the-single-most-usefull-php-trait-i-use-in-my-projects-41l8>

我经常用这个把一个类转换成数组和 json。我想我应该把它贴在这里，它可能会帮助一些人。😀

```
trait Serializer
{
    public function toArray()
    {
        return get_object_vars($this);
    }
    public function toJSON()
    {
        return json_encode($this->toArray());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 如何使用。

假设您有一个`Person`模型，并且希望在代码中的某个地方以数组或 json 的形式访问它。

```
class Person
{
    use Serializer;
    private $id;
    private $name;
    public function setName($name)
    {
        $this->name = $name;
    }
}

$person = new Person();
$person->setName("Isaac");

$arr = $person->toArray();
print_r($arr);

$json = $person->toJSON();
print_r($json); 
```

Enter fullscreen mode Exit fullscreen mode

我发现这在开发 rest api 时非常方便。